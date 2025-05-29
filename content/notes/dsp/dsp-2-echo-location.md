+++
date = "2023-09-27"
draft = false
tags = ["dsp"]
title = "DSP 2: Echo Location"
+++

Three applications for DSP involving echo location:

**Radar** (RAdio Detection And Ranging): works by firing a pulse of radio waves at an area you want to investigate. If something's there, it'll reflect the radio wave back to you.

The longer the pulse, the more energy you have, but the less accurate you are. DSP helps here in a couple of ways: return signal compression, filtering noise, and pulse selection and generation. All of this is further aided by samplers that work on the order of megahertz, which is impressive!

**Sonar** (SOund NAvigation and Ranging): works similarly to radar, but in water. It's divided into two groups: active sonar emits pulses with frequencies between 2kHz - 40kHz, whereas passive sonar simply listens for activity. DSP helps in similar ways to radar - filtering, generation, combining, and so on.

**Reflection seismology:** This entails setting off explosions and measuring how the echoes are returned by layers of the earth, up to 10km beneath the surface. This practice made it possible to detect petroleum and minerals beneath the surface, by their unique seismic signal - more generally, it allowed us to map the earth's crust. However, the returned echoes are bouncing like crazy between layers of earth, so the echoes make more echoes. Filtering and processing these signals via DSP allows us to tackle some complex areas for investigation. Before DSP, geophysicists were pretty limited in how much geophysical complexity they could tackle, and mostly looked for oil in "easy" places.