# #815 - Documentation mentions the possibility of linking boost in as a static or dynamic library, but offers practically zero details on how to do so. [Closed]

> Username: ghost  
> Created at: 2022-11-30 23:24:01 UTC  
> Updated at: 2022-12-04 16:57:07 UTC  
> Closed at: 2022-12-01 07:23:31 UTC  
> Url: https://github.com/boostorg/json/issues/815  

### Problem  
The overview mentions the possibility of linking boost::json in as a static or dynamic library, but offers practically zero details on how to do so. I've heard that you just chuck -lboost_system at your linker when you're building the executable, but this rumor is not confirmed by any of the documentation, and didn't seem to work for me (see error below). Do I have to build the library? Should I have installed boost with my package manager instead of getting the latest version directly from the boost website? Perhaps detailed instructions on how to build and link boost::json are outside the scope of the overview, but it would be helpful if there were a link to something that explained how to do it.  
  
### Steps necessary to reproduce the problem  
  
Visit the boost::json docs [overview](https://www.boost.org/doc/libs/1_80_0/libs/json/doc/html/json/overview.html).  
  
### All relevant compiler information  
Platform/Architecture: 64-bit Linux Mint 21 (Cinnamon)  
Compiler: g++ 11.3  
Error output: `/usr/bin/ld: cannot find -lboost_system: No such file or directory`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-11-30 23:26:39 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1332870214  

> detailed instructions on how to build and link boost::json are outside the scope of the overview  
  
Yes, because there are many different ways to do this, and many different toolchains. But the easiest thing to do is just add this line to ONE of your new or existing .cpp files:  
  
```  
#include <boost/json/src.hpp>  
```  
  
and then you're done, no need to fiddle with building libraries or passing command line options. This is in the docs:  
https://www.boost.org/doc/libs/1_80_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only  
  
As for getting Boost, that is also documented:  
https://www.boost.org/doc/libs/1_80_0/more/getting_started/index.html

---

## Comment 2

> Username: ghost  
> Created at: 2022-12-01 00:39:51 UTC  
> Updated at: 2022-12-01 01:54:37 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1332957665  

That's what I was using as a workaround, but if the option is presented, I should at least be given a place to start searching for answers right from the boost::json getting started page.  
  
The second link you provided is fantastically helpful! It would fit perfectly within the boost::json getting started page. Before you showed me that link, I didn't know what to pass to my linker or whether or not I have to build the library.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-01 05:35:50 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1333223050  

Yeah, information on how to link Boost libraries is centralized. Rather than duplicating the same information for each library. You can also put together your own static library using your own build scripts by including the src.hpp file. This is very useful if for example you are not using b2 or cmake.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-12-01 07:23:31 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1333320265  

The information on how to build Boost is presented in the Boost documentation (https://www.boost.org/doc/libs/1_80_0/more/getting_started/unix-variants.html).  
  
Boost.JSON assumes the reader of its documentation is proficient enough in C++ to make programs. This includes knowledge of how linking works in C++ and how that user's compiler and linker is invoked and how to control their behaviour (e.g. compiler/linker flags). Assuming otherwise would be insanity.  
  
We mention that the library can be built as a static and shared library, because that's not true for some libraries. We also mention that the library can be used without linking (header-only) and describe how to do it, because the trick is specific to this library, and not a feature of compilers or linkers.  
  
>  I've heard that you just chuck -lboost_system at your linker when you're building the executable, but this rumor is not confirmed by any of the documentation, and didn't seem to work for me...  
  
This does work for me, because I've built Boost in a certain way. This may not work for you if you've built it in a certain different way. It depends on a million different things, including what linker you are using, because I assure you msvc does not use `-l` flag.  
  
> Do I have to build the library? Should I have installed boost with my package manager instead of getting the latest version directly from the boost website?  
  
Wait, did you just download Boost sources and did not build Boost? What are you linking to then?  
  
To summarize:  
* Getting started with Boost docs are: https://www.boost.org/doc/libs/1_80_0/more/getting_started/index.html.  
* Compiler and linker docs are out  out of scope.  
* C++ linking model docs are out of scope.

---

## Comment 5

> Username: ghost  
> Created at: 2022-12-02 15:03:57 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1335381823  

Why would you assume I'm on msvc? Why would you assume that I don't know how a linker works? All I ask is that [https://www.boost.org/doc/libs/1_80_0/more/getting_started/index.html](https://www.boost.org/doc/libs/1_80_0/more/getting_started/index.html) is included in the boost::json getting started page.  
  
"Boost.JSON assumes the reader of its documentation is proficient enough in C++ to make programs." Elitism is alive and well in every facet of C++ it seems.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-12-02 17:22:30 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1335562225  

> All I ask is that https://www.boost.org/doc/libs/1_80_0/more/getting_started/index.html is included in the boost::json getting started page.  
  
Yeah, linking to that page is probably a good idea. I only didn't do it because I'm not sure how to form the proper "green link" (one that automatically links to the same version).  
  
> "Boost.JSON assumes the reader of its documentation is proficient enough in C++ to make programs."  
  
I mean... you're not entirely wrong here but I don't think its fair to call this elitism. If every library developer had to solve every cross-cutting concern (like informing users how to link programs for every toolchain) that would produce a combinatorial explosion of duplicated effort.  That said, we should add the link :)

---

## Comment 7

> Username: ghost  
> Created at: 2022-12-02 18:08:47 UTC  
> Updated at: 2022-12-02 18:17:10 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1335626557  

I took that quote as elitisim because @grisumbras clearly did not even read the post, since he assumed I use msvc despite the fact that I mentioned:  
> Platform/Architecture: 64-bit Linux Mint 21 (Cinnamon)  
> Compiler: g++ 11.3  
> Error output: `/usr/bin/ld: cannot find -lboost_system: No such file or directory`  
  
This means that he commented on the thread and closed it expressly to put someone down for having less experience than him in one particular aspect of writing C++ programs. That is elitism.  
  
Saying things like  
> I assure you msvc does not use `-l` flag.  
  
> Wait, did you just download Boost sources and did not build Boost? What are you linking to then?  
  
can in no way be interpreted as helpful, constructive, or anything other than contemptuous. By the way, my question was how to link and whether or not I need to build Boost, once again proving he didn't read a word of the issue.  
  
Thank you for productively addressing my suggestion.

---

## Comment 8

> Username: pdimov  
> Created at: 2022-12-02 19:52:47 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1335764193  

The documentation could stand to be improved on this particular point. Usually people can just apt-get their Boost, but distributions often carry an earlier release, looks like 1.75 in Mint 21's case, which won't have Boost.JSON. So a paragraph about building would help, even if all it does is to link to the getting-started page.

---

## Comment 9

> Username: grisumbras  
> Created at: 2022-12-03 15:35:34 UTC  
> Updated at: 2022-12-03 15:35:53 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1336184210  

> I took that quote as elitisim because @grisumbras clearly did not even read the post, since he assumed I use msvc…  
  
I did not assume that, you did not read what my response very well. MSVC was only brought up as an example of a toolset that doesn't use the flag `-l`, to illuminate the fact that listing specific flags for specific compilers and linkers is not very useful.  
  
What I did assume is that you don't know what linking is, because you write  
  
> The overview mentions the possibility of linking boost::json in as a static or dynamic library  
  
And then you write  
  
> Do I have to build the library?  
  
Yes, in order to link to a library you need to build it. Otherwise what would you specify when you invoke the linker?  
  
In general, I have a negative view of issues that complain about documentation quality, while simultaneously display lack of proper understanding of the things discussed in that documentation. If you'd phrased your issue as a request for help instead, I would have handled it differently.  
  
Anyways, I do admit that linking to "Getting Started with Boost" will be an improvement. Did it help you link to the library @mcmuffin6o?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-12-03 16:54:36 UTC  
> Updated at: 2022-12-03 16:54:43 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1336197446  

I think  
> Do I have to build the library?  
  
was asking if the Boost distribution comes with binaries (it doesn't).

---

## Comment 11

> Username: ghost  
> Created at: 2022-12-04 16:31:24 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1336460135  

> Yes, in order to link to a library you need to build it. Otherwise what would you specify when you invoke the linker?  
  
@vinniefalco seems to have answered this question by understanding that some libraries come with binaries. I can't see how you accuse me of not understanding enough about building/linking libraries without understanding the above yourself @grisumbras:  
  
> In general, I have a negative view of issues that complain about documentation quality, while simultaneously display lack of proper understanding of the things discussed in that documentation.  
  
I don't see how my particular criticism of the docs could be considered unfounded @grisumbras, especially since it seems like everyone agrees that adding a link to "Getting Started with Boost" closes this issue (even though you closed it before addressing my suggestion to do so).  
  
It also doesn't help when people that don't have much experience building and linking libraries get this as their first web search hit: https://stackoverflow.com/questions/10262742/linking-boost-libraries  
  
(As you may notice, I added the Boost Getting Started guide so that others don't come here to open similar issues.)  
  
> If you'd phrased your issue as a request for help instead, I would have handled it differently.  
  
I think your best approach would have been to not handle the issue at all.  
  
Your peers did a fantastic job of handling the issue. If this thread was purged of all of your input, we would have settled on the agreed-upon solution before closing the issue, instead of after. Because of your aggressive approach towards pruning unfounded issues, I have wasted hours of my time and some of yours just trying to establish validity for my issue when it was recognized almost immediately by the others after a short, productive exchange.  
  
> Anyways, I do admit that linking to "Getting Started with Boost" will be an improvement. Did it help you link to the library @mcmuffin6o?  
  
Nope, I gave up after seeing how much of a fight was involved in even getting to that point. I've wasted too much time on this thread and I just need to finish my research. I had been using the header-only approach until I ran into multiple-inclusion issues, at which point I wanted to link the library and instead include a proper header that only had what the compiler needed to know about the types and functions of the library. I guess I should also mention that it was difficult to figure out what that header file was until I checked the examples. This could just be on me, but I never noticed any instructions for what header to include in source files when you have multiple source files using the library.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-12-04 16:55:21 UTC  
> Updated at: 2022-12-04 16:55:41 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1336466286  

To link without having to build a separate library, add this line in exactly ONE .cpp file:  
```  
#include <boost/json/src.hpp>  
```  
  
(if you are on Windows you have to also define `BOOST_JSON_NO_LIB` for your project)

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-12-04 16:57:07 UTC  
> Url: https://github.com/boostorg/json/issues/815#issuecomment-1336466578  

> our peers did a fantastic job of handling the issue.  
  
I also have the benefit of 30+ years of experience :) Interacting with other developers diplomatically is a skill...
