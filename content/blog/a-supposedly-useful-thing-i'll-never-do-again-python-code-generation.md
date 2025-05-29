+++
date = "2021-11-08"
draft = false
tags = ["tech"]
title = "A Supposedly Useful Thing I'll Never Do Again: Python Code Generation"
+++

New team, new language, new me. Security + Python. Learning Python is going well, although I don't really like the language for so many reasons. But feeling good.

Feeling wise -- recent staff engineer, and armed with the tentative insight that what makes me a good engineer is the tendency to ask "does this exist? do we need to write code for this?".

Assigned a new task - an automation platform for cloud operations. Okay, let's do it. FastAPI, OpenAPI, automated tests, boom. Everything is FLOWING. My team seems delighted.

Now comes the useful part - the platform simply stitches together backend API calls and exposes those chained calls into a meaningful, easy-to-use API for users and other services.

I start looking through the docs for the backend API. There's auto-generated docs, sample JSONs, the usual mature stuff for this kind of backend. So I could copy these sample JSONs, read the docs, make some data classes, copy / paste some validations, write the methods, and off to the races.

But no. I'm wiser than that - I'm a ~STAFF~ engineer. Hell yea. There's gotta be some SDKs for this, right? Of course there are for this backend API. Java, C#, and ah, there it is, Python. Boom.

And this C# SDK, it look like it has auto-generated models! Of course it does, based on the docs I've been looking at. Hey, there's a schema spec somewhere out here! It's used for auto-generating SDK types, and having meaningful, typed, validated types in your language of choice. It's probably used for serialization. Awesome.

But what's this?! The Python SDK doesn't have these types! And it doesn't seem like it's getting much love! Well, that's no good. I start to use it anyway, and think - I could write a simple client and provide these types. And I can even provide these types from the schema spec if I find it!

I do. I find it. It's a huge XML doc, but who cares, I only need my type, right? I do some parsing magic on my CLI, some little perl and awk one-liners, and I've got what I need! Oh man. This is going awesome. I'm a staff software engineer. Am I also...the best programmer ever? (Narrator: he wasn't.)

Why stop there?! Let's fly closer to this burning sun of complexity! My one-liners turn into scripts to generate the Python code. It's standardized. I can do this for ANY type. Holy heck. I could just write this client?! What if I do this in Python? Can I open-source this client? Am I better at Python than I thought? Forget caution, I'm in the ZONE. I'm flowing.

I find XML parsing libraries. I parse the tree, extract out the type I want. I use Jinja2 for templating, and have a class definition. I tell some teammates that I've got types for free. It's true. I do. My client delegates metadata on how to call a resource into the type itself, instead of just making massive client sets. Write the type, get all RESTful operations on that type for free. I really might be just, like, the best programmer ever, I think.

But of course, the attributes of my top-level class have types of their own, and if I work with the types, I can get really effective SerDe for initialization! Okay, so I need to parse the attribute types, find those in the schema spec, find if they have any new types themselves. Okay okay, this is getting hard - recursion always is - but I can do this. I've been dealing with recursion since the Tower of Hanoi in 11th grade.

I write the code. It's long, it's messy. That's ok, I'll refactor it. I'm a staff software engineer. I leave copious comments for future me, and even make the JIRA tickets. It's not a high priority. It may feel good, but I'm a mature employee who knows when to stop.

And my code works! I've recursively discovered and templated all subtypes for a single top-level type. I leave some things out though - the schema spec has these fields called Navigation Properties that seem unneeded and a little complex. I'll worry about it if I have to. I'm a staff software engineer - I don't get carried away.

I generate my types. I use them in my API. They work. I write tests, I complete a ton of tickets. I worry about docs and security and usability and maintainability. I push left. I try to ignore the 400 lines of unmaintainable recursive garbage I just wrote to generate types. It's okay - it works, I won't need to revisit it for awhile, and I have comments and tickets and my mature wisdom on how to handle this. I am a STAFF. SOFTWARE. ENGINEER.

More user-facing APIs are implemented. I knock them down quick. I generate more types. Complexities start creeping in. Some types are nullable. The schema spec has inheritance baked in. I have to map from schema types to Python types. I have to deal with imports; snake casing vs. Pascal casing vs. camel casing; Jinja2 has some missingness trickiness I have to learn. That's okay. My time spent on the type generator is creeping up - a week of work hours at this point? But that's ok. It's worth it. I'm not getting carried away.

But I start to get worried. Doubt creeps in. This is so much more than I bargained for. Why didn't I expect this? Why didn't I email the owners of the back-end API immediately, ask them why their Python SDK is so weak? What did they find out that I don't know? How much more time am I going to lose to this?

Begrudgingly, I write up a beautiful email to the backend API owners. Ask them all the questions I should've asked immediately. I don't get a response.

I refactor more code. I use best practices of refactoring. I create data structures with meaningful, small APIs in their method set (not that Python cares about exported vs. unexported). I roughly use patterns - mediators, creational patterns. I use Python things too - listcomps and class methods and some metaprogramming. The code slowly, brutally yields to the hours. I'm still flowing, but it's got a different hue to it -- rusted out, tired, unsure of the end goal.

Finally - I find a user-facing API that requires those Navigation Property fields I ignored either. Fine. I generate those types, it goes okay. I run my tests, and WHAM - unable to import from a partially initialized module. I look at the schema doc more closely - wait, these Navigation Property fields can contain themselves?

Oh no.

I wrack my brain. I look at the other SDKs - C# has namespaces, which allows for magical semi-global scope, beautifully circumventing the problem of a dependency graph. Not an option here, I don't think. I try anyway. A day passes. Two, three. I code 6-8 hours a day with diminishing returns each day. My sweatpants need washing. I keep eating lunch too late. It's fine I'm a staff software engineer everything is fine.

I rewrite my generator code one last time. I don't need to generate all types, just the ones my top-level type requires. I laser-focus my recursion, make my exit condition a sort of quiescence of type discovery, a set stability. No imports means no loops, right?

But wait - anything in your __init__ can reference other types in the same file with no dependency graph involved, because it's a method and isn't called on import. But not your parent types, for this weird inheritance.

Oh no no nooooooo.

I stare at my computer in sadness, contemplating my defeat. Total time spent is approaching three weeks. I have a working client, I do. But I had that a week ago, and I didn't think it was good then. I think it's worse now. 

I fork off my git branch, push it up with a defeated commit message, making up a conventional commit tag of "failed-wip". I revert numerous commits. I go back to last week. I follow up with the backend API team. 

Never again. I got so excited, you guys. I thought I could do it, implement this beautiful Python client that worked off generated Python types.

I sure learned a lot technically, but more than anything else - I learned **DON'T do that.** If a problem seems complex, it probably is. If an owning team hasn't solved it for you, there's probably a good reason for that. Find out that reason. Email the API owners. Follow up. Get your manager to yell at them. Do this all BEFORE you drop 2-3 weeks of difficult coding into the endeavor.

I tried to auto-generate a Python client, and all I got was this weird blog post. Sigh. Back to keeping it simple, stupid.