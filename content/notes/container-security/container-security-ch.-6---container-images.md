+++
date = "2024-10-10"
draft = false
tags = ["container-security"]
title = "6 - Container Images"
+++


**Container images help us create containers.** They are typically stored in a registry. How we build, store, and retrieve images is crucial for container security.

There are two parts to a container image: your root filesystem and your configuration. Per the [OCI standard](https://opencontainers.org/about/overview/), there can also be an image manifest listing sources and dependencies. Tools like [skopeo](https://github.com/containers/skopeo) (working with image registries) and [umoci](https://github.com/opencontainers/umoci) (creating and interacting with OCI images) all work with the OCI standard, which has been around since 2015. Using umoci, we can see an image's config.json contains very direct data about cgroups and Linux namespaces.

Docker uses containerd, which now supports running in a rootless mode, but implementation is ongoing. A long-running daemon running with root privileges to support Docker's lifecycle management is a security risk - container engines like Podman were meant to work without requiring root. [Buildkit](https://github.com/moby/buildkit), from the [moby](https://mobyproject.org/)[ project](https://mobyproject.org/), also supports rootless builds.

Images are built in layers, making use of Linux's [OverlayFS](https://docs.kernel.org/filesystems/overlayfs.html) (a union mount filesystem) and the containerd [overlay2](https://docs.docker.com/engine/storage/drivers/overlayfs-driver/)[ driver](https://docs.docker.com/engine/storage/drivers/overlayfs-driver/). You can inspect layers for Docker using docker image inspect; every Dockerfile command that takes up space will create a new layer. There are options to squash image layers via the Docker CLI. **Don't store secrets in one layer and delete it later, thinking you're safe** - layers are immutable and accessible.

Images are stored in registries. There's an OCI Distribution Specification for working with registries. Each layer is stored as a blob, uniquely identified by a hash of its contents. The whole image has an image digest (just a hash of the image manifest). You can also tag images. Tags are mutable; the image digest is immutable. You can pull images by their digest for stronger reproducibility guarantees. Image scanners might be tricked by a changing tag - if the image changes and the tag doesn't, an image scanner admission controller might let the image through without a scan, thinking it's already scanned the image.

## Best Practices:

- Use trusted registries (or your own), and "golden" base images (not random third-party ones).
- Use small images, like [distroless](https://github.com/GoogleContainerTools/distroless), or just use [scratch](https://docs.docker.com/build/building/base-images/#create-a-minimal-base-image-using-scratch), although getting your dependencies right might be annoying.
- Use [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) - build requirements are different than runtime requirements.
- Use a non-root user in the container.
- Don't mount sensitive host directories like /etc/.
- Include everything your application needs baked into the binary - don't download dependencies right before / at runtime.
- Sign your images.
- Adhere to strict versioning if using tags. Don't play with mutability.
- Your build machine's security is as essential as your production cluster's.