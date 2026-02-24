# #121 add boost_core.natvis and interface project sources [Closed]

> Username: vinniefalco  
> Created at: 2022-08-18 22:48:20 UTC  
> Updated at: 2022-08-20 15:20:44 UTC  
> Closed at: 2022-08-19 15:16:24 UTC  
> Url: https://github.com/boostorg/core/pull/121  



---

## Comment 1

> Username: Lastique  
> Created_at: 2022-08-19 08:11:52 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220382350  

Please no IDE projects. And definitely not in the `include` directory.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-08-19 12:40:57 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220629197  

boost_core.natvis is not an interface project, it is a debugger visualizer.

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-08-19 12:55:56 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220644193  

Either way, `include` is not the place.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-08-19 13:12:42 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220663950  

> Either way, `include` is not the place.  
  
This is confusing to me, because include/ is what is installed when you "install" Boost from a package, and those files are become available to users. You don't want Visual Studio users to have access to the debugger visualizer?

---

## Comment 5

> Username: Lastique  
> Created_at: 2022-08-19 13:25:57 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220681059  

1. I would like Visual Studio visualizers to be distributed with Visual Studio or as a separate package entirely. I do not like the idea of distributing projects and config files for every IDE and debugger under the sun as part of Boost. A Boost distro should be a minimal set of sources that can be built to binaries, everything else is optional and should be available on-demand separately.  
2. `include` is the directory with C/C++ headers, it gets installed in `/usr/include` on POSIX systems, and it is no place for config files such as this. I would guess, stuff like this should go into `/usr/share` somewhere, or, more precisely, to the location where the given debugger expects it. Which should not be `/usr/include` anyway. We in Boost don't have an established place for such things, so it should probably be a new top-level directory that downstream packagers will have to support.

---

## Comment 6

> Username: Lastique  
> Created_at: 2022-08-19 13:39:45 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220696417  

A few examples of auxiliary stuff in libraries: in [Boost.QuickBook](https://github.com/boostorg/quickbook/tree/develop/extra/), in [Boost.Container](https://github.com/boostorg/container/tree/develop/proj), in [Boost.Histogram](https://github.com/boostorg/histogram/tree/develop/tools).

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-08-19 13:43:29 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220700239  

Should I move it to a new folder named extra/ in Core? Or would you rather I simply close this?

---

## Comment 8

> Username: Lastique  
> Created_at: 2022-08-19 13:45:32 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220702211  

Personally, I would rather close it. But if others think this is a useful addition, it should be in `extra`, definitely not `include`.

---

## Comment 9

> Username: Lastique  
> Created_at: 2022-08-19 13:47:37 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220704267  

Also, the CMake modification is not necessary and should be removed.

---

## Comment 10

> Username: glenfe  
> Created_at: 2022-08-19 14:27:36 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220745008  

I want include/ to contain only headers. I'm fine with extra/ though.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-08-19 15:16:24 UTC  
> Updated_at: 2022-08-19 15:30:53 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220796500  

> Also, the CMake modification is not necessary and should be removed.  
  
The "CMake modification" generates a interface project containing the headers and the natvis file. For normal builds, this doesn't do anything. But if you generate a Visual Studio solution then it creates a proper project:  
  
![image](https://user-images.githubusercontent.com/1503976/185648722-6b3a653d-ef5a-4afa-8c63-398636dff0c2.png)  
  
What's the point of this, you ask? Well first of all, Find in Files from the IDE will be able to search within Boost.Core's headers. Second, anyone who generates a project that depends on Boost.Core will automatically have the .natvis file loaded and available upon launching a debug session.  
  
I'm going to simply close this, because I'm 50 years old now and I don't have the energy to argue about these things like I did in my youth. Besides, I don't have a lot of time left, so this problem will "resolve" itself in what - 30 or 40 years if I'm lucky? And that's assuming I continue writing C++ until my death, which is unlikely.  
  
However before I do that I will explain my rationale. Boost is already seen as an inconvenience by many users especially those on C++17 or later. The value proposition for users has been steadily diminishing. We're asking people to accept `boost::core::string_view` over `std::string_view`. When I look at `std::string_view` in my IDE I see a perfectly normal string. But when I look at a `boost::string_view` I see the wrong thing. For example consider this:  
  
```  
boost::string_view s = "The rain in spain";  
s.remove_suffix(  14) );  
```  
  
After these statements, the debugger shows "p_ = The rain in spain, n_ = 3" for Boost, and for `std::string_view` it correctly shows "The". With my natvis file, Boost's string view shows "The".  
  
These visualizers, which I have written for Boost's `variant2`, `result`, `error_code`, `error_condition`, `source_location`, and `string_view` (as well as all of my own libraries) are my way of trying to add some value back in. True, it is only for Visual Studio users and then only if they use a generated project. I don't have any metrics for you on how many people would benefit, the truth is I don't know. Perhaps with the new website we will have a means of surveying to get these results.  
  
My point is this: if we leave the natvis files out of the repo completely, and require users to download them from some plugin side, no one is going to do that and there will be no value add. If we remove the changes to the CMakeLists.txt and put the natvis files in extra/ lets be honest, it will be basically invisible, no one will know or bother to install it, and there will be no value add. I mean if we're being honest, every Boost library comes with readily available documentation on hand and many people don't even read that so the idea that folks are going to go the extra mile and configure their local project to use an out of the way visualizer is effectively nil.  
  
With my changes, users of Visual Studio will automatically benefit, and there is some chance that when they update to this new version of Boost they will be pleasantly surprised that the debugger now displays variables in more meaningful and profound ways. No one is going to look at Boost and praise it for not having natvis files in include directories or not adding sources to interface-only projects in the CML. But someone might stumble onto the natvis support and be pleasantly suprised to see the level of attention to detail, and praise/recommend Boost based on that.  
  
Yes, I know this is only for Visual Studio users.  
Yes I know this is only when they use the IDE.  
Yes I know this is only when they use the debugger  
Yes I know this is only when they generate a solution/project file  
No there are not yet visualizers for other platforms, and I have no plans to write them  
  
To conclude I reiterate that I have no statistics relating the quantity of benefit that we get out of this but I have considerable evidence that users view Boost's parallel set of types as an annoyance. What my changes offer is a way to restore some of the lost value. This is not my library so the decision on whether or not adding value to some unknown fraction of users has merit.

---

## Comment 12

> Username: pdimov  
> Created_at: 2022-08-19 15:25:17 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220805050  

The release procedure only copies headers from the `include/` directory, so the .natvis files, if put there, aren't going to be present in a release archive anyway.  
  
What happens if the .natvis is put in `extra/` and is included in the Visual Studio project? Does the debugger recognize and load it?

---

## Comment 13

> Username: Lastique  
> Created_at: 2022-08-19 15:51:13 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220829064  

> I mean if we're being honest, every Boost library comes with readily available documentation on hand and many people don't even read that so the idea that folks are going to go the extra mile and configure their local project to use an out of the way visualizer is effectively nil.  
  
If this config is useful, people will use it. If people don't want to be bothered to use it then maybe it's not very useful to them.  
  
My issue with the CMake change is that it (a) globs the sources, which is not a good idea and may be problematic and (b) it references the sources that are not needed or even valid on most platforms. If you want to add these files specifically to the Visual Studio project then that source file must be restricted to that specific CMake generator.

---

## Comment 14

> Username: pdimov  
> Created_at: 2022-08-19 16:04:26 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220841073  

I don't think any generator has a problem with adding the headers as sources to the interface project. Non-IDE generators would just ignore them. (That ability has been added to CMake precisely for this use case.)  
  
Creating projects for header-only libraries is admittedly a bit of a slippery slope - we definitely don't want this for all libraries - but if we only restrict it to libraries with debugger visualizers, it will be manageable.

---

## Comment 15

> Username: Lastique  
> Created_at: 2022-08-19 16:09:25 UTC  
> Updated_at: 2022-08-19 16:17:25 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220849221  

> I don't think any generator has a problem with adding the headers as sources to the interface project.  
  
I'm not talking about headers - adding those is fine and even recommended when the IDE can use CMakeFiles.txt directly. I'm talking about adding the natvis config. It is only useful for Visual Studio, so it should be added only there.  
  
BTW, is adding the config to the project enough to get it used by Visual Studio debugger? That is, does merely adding it to the project enable it? If the user has to enable it some other way, like a menu or something, then adding it to the VS project is also kind of pointless.

---

## Comment 16

> Username: pdimov  
> Created_at: 2022-08-19 16:17:15 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220858054  

Yes, I agree that adding the natvis to the project can and probably should be guarded.

---

## Comment 17

> Username: pdimov  
> Created_at: 2022-08-19 16:22:30 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220862484  

Yes, adding the natvis to the project makes it available to the debugger (and a path to it is embedded in the .pdb so the debugger will find it even if the project isn't open.)

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2022-08-19 16:48:17 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1220884803  

> What happens if the .natvis is put in extra/ and is included in the Visual Studio project? Does the debugger recognize and load it?  
  
Yes, this works.

---

## Comment 19

> Username: sehe  
> Created_at: 2022-08-20 15:20:44 UTC  
> Url: https://github.com/boostorg/core/pull/121#issuecomment-1221333959  

> Second, anyone who generates a project that depends on Boost.Core will automatically have the .natvis file loaded and available upon launching a debug session  
  
That's a feature and I'm here for it. Even though I kinda hate using Visual Studio :) These are the rare things that actually make it worthwhile. I will never go and find out how to add visualizers - I only have managed half-broken gdb support to date, so things "just working" is definitely a selling point.

---
