# #795 - X3 documentations should be more visible (Splitting Spirit repositories) [Closed]

> Username: Flamefire  
> Created at: 2024-10-12 10:13:15 UTC  
> Updated at: 2025-10-30 19:06:27 UTC  
> Closed at: 2025-09-13 01:13:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/795  

When I open the documentation spirit 2 is shown: https://www.boost.org/doc/libs/1_86_0/libs/spirit/doc/html/index.html  
  
Only a remark-like comment at the bottom reads:  
> For the newest (C++14) version, please follow to [Spirit.X3](https://www.boost.org/doc/libs/1_86_0/libs/spirit/doc/x3/html/index.html)  
  
I found that a bit hard to find when looking for the "normal" documentation.

---

## Comment 1

> Username: ribbon-otter  
> Created at: 2025-03-21 03:41:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-2742168567  

I would like to second this. I only learned the spirit 2 was not the most recent version because I was looking for exact source code so I could read the headers.

---

## Comment 2

> Username: saki7  
> Created at: 2025-09-06 23:20:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3263279661  

We actually need to make the root index point to X3, and link V2 from there (suggested by https://github.com/boostorg/spirit_x4/issues/15)).

---

## Comment 3

> Username: apolukhin  
> Created at: 2025-09-10 10:30:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274331070  

It would be nice to have the Spirit.X3 as a separate library, without all the legacy of classic Spirit.  
  
Some modern projects try hard to reduce dependencies and watch the module dependencies at https://pdimov.github.io/boostdep-report/develop/module-levels.html. Moving the X3 into a separate repo would drop most of the pre C++17 boost::* dependencies, making the X3 much more attractive to users

---

## Comment 4

> Username: saki7  
> Created at: 2025-09-10 10:42:51 UTC  
> Updated at: 2025-09-10 10:43:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274366804  

@apolukhin Thanks for your attention. We once had conversation with Peter Dimov and he also said we should split subcomponents. However, at the beginning of that conversation, I insisted on _removing_ Spirit.Classic (after certain period of deprecation period) and thus the debate heated up too much. Eventually we had a consensus that we're going to **split** (not remove) Spirit.Classic. [Peter told us](https://github.com/boostorg/spirit/discussions/802#discussioncomment-13098351) that it's going to be easier if we let Peter do the actual work, but the migration didn't take place since then. Perhaps Peter is busy.  
  
My current opinion is that we should split all Spirit subcomponents into distinct repositories:  
  
- Classic  
- V2 (Qi, Karma, Lex)  
- X3  
  
@apolukhin I want to make this happen. What can we do right now? Should we wait for Peter?

---

## Comment 5

> Username: apolukhin  
> Created at: 2025-09-10 10:51:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274408223  

Let's ask @pdimov :)  
  
Peter, is it OK for @saki7 to make separate repos in `boostorg/` to separate old and modern Spirit implementations?

---

## Comment 6

> Username: saki7  
> Created at: 2025-09-10 10:53:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274414807  

FYI, Spirit.X3 is now totally self-contained thanks to boostorg/spirit#817.  
Classic was already self-contained thanks to Joel's historical efforts.

---

## Comment 7

> Username: pdimov  
> Created at: 2025-09-10 11:25:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274526389  

I already forgot everything we discussed, so could you please bring me up to date? What is the specific split we're considering, that is, how should the split repositories be named, and what would their directory structure be?

---

## Comment 8

> Username: saki7  
> Created at: 2025-09-10 11:40:34 UTC  
> Updated at: 2025-09-10 11:41:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274570182  

The only explicit consensus we have is that we split Classic into the separate repository. But that consensus was made 4 months ago and the situation has changed. I recently managed to make X3 self-contained too, so I'd suggest splitting all 3 subcomponents into 3 distinct repositories:  
  
- `boostorg/spirit-classic`  
- `boostorg/spirit-v2`  
- `boostorg/spirit-x3`  
  
The only subcomponent that has trivial content is Classic, which consists of `classic/*`, `include/boost/spirit/home/classic/*` and `include/boost/spirit/include/classic*.hpp`.  
  
V2 and X3 are both self-contained in C++ source code level, but they still share the same parent directory, e.g. `test/` and `doc/`. So if you demand the comprehensive list of files, that requires a lot of communication and I believe it's nonsense to do that.  
  
If you can give me the organization-wide privilege then I can do all above work myself. If you're afraid of giving too much permission then I'd suggest creating 2 empty repositories (`boostorg/spirit-classic` & `boostorg/spirit-v2`) then give me full permission only for those.

---

## Comment 9

> Username: saki7  
> Created at: 2025-09-10 12:30:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3274748927  

If you choose to do the migration yourself then that's fine, I won't take it personally. But I think this should be done by a single person due to the communication cost.

---

## Comment 10

> Username: pdimov  
> Created at: 2025-09-10 14:40:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275294146  

Our repositories correspond to directories under `libs/`, and as a rule we don't use dashes in those. Assuming underscores, the repos above would correspond to  
  
libs/spirit_classic  
libs/spirit_v2  
libs/spirit_x3  
  
Is that what we want? What about the currently existing libs/spirit, would that be deleted?

---

## Comment 11

> Username: saki7  
> Created at: 2025-09-10 14:59:58 UTC  
> Updated at: 2025-09-10 15:00:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275368327  

> libs/spirit_classic  
> libs/spirit_v2  
> libs/spirit_x3  
>  
> Is that what we want?  
  
Yes, this looks good to me.  
  
My suggestions:  
  
- Keep the current repository `boostorg/spirit` as-is for a while  
- Clone everything to `boostorg/spirit_classic`  
  - `git rm -rf` all files except those related to Classic  
- Clone everything to `boostorg/spirit_v2`  
  - `git rm -rf` all files except those related to Qi/Karma/Lex  
- Make sure the ordinary Boost Organization infra is working for the new 2 repositories (e.g. static site generation)  
- Rename `boostorg/spirit` to `boostorg/spirit_x3`  
  - `git rm -rf` all files except those related to X3  
  
This way we keep all history with compatible hash, among all 3 repositories. This process minimizes the downtime as well.

---

## Comment 12

> Username: pdimov  
> Created at: 2025-09-10 15:18:58 UTC  
> Updated at: 2025-09-10 15:19:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275439199  

To clarify once again, in the Boost 1.89 release tarball we have `libs/spirit` and after the proposed changes, in the Boost 1.90 release tarball we won't have `libs/spirit`, but `libs/spirit_classic`, `libs/spirit_v2`, and `libs/spirit_x3`, right?  
  
That's probably going to be relatively disruptive. Two things that come to mind: (1) the web site doesn't cope well with removed libraries, because it can show previous releases, e.g. https://www.boost.org/doc/libs/1_64_0/libs/spirit/doc/html/index.html, so it has to retain everything; (2) every CMakeLists file linking to Boost::spirit will now be broken.  
  
I'm not sure what better alternative to propose, though. Maybe we could keep `libs/spirit` as some sort of a stub library?

---

## Comment 13

> Username: apolukhin  
> Created at: 2025-09-10 15:23:51 UTC  
> Updated at: 2025-09-10 15:25:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275460247  

+1 for keeping the `spirit/` as the most modern Spirit.X3. Such move should give the users the signal to use it by default. The legacy libraries should move out into spirit_classic and spirit_old_v2  
  
But I'm only an observer here, words of @saki7 have more weight

---

## Comment 14

> Username: pdimov  
> Created at: 2025-09-10 15:27:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275474413  

My style would be to keep `spirit` the classic one and add `spirit2` and `spirit3`, but that's obviously not the preference here. :-)

---

## Comment 15

> Username: apolukhin  
> Created at: 2025-09-10 15:30:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275482793  

+1. That also gives a good signal to use spirit3 and also keeps most of the backward compatibility

---

## Comment 16

> Username: saki7  
> Created at: 2025-09-10 15:42:39 UTC  
> Updated at: 2025-09-10 15:43:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275529150  

> +1 for keeping the `spirit/` as the most modern Spirit.X3. Such move should give the users the signal to use it by default.  
  
I actually think this is a good idea. So my preference is:  
  
- `boostorg/spirit` -- keep, X3  
- `boostorg/spirit_classic` -- new repo, Classic  
- `boostorg/spirit_v2` -- new repo, Qi & Karma & Lex (& `/repository/`)  
  
> (1) the web site doesn't cope well with removed libraries, because it can show previous releases, e.g. https://www.boost.org/doc/libs/1_64_0/libs/spirit/doc/html/index.html, so it has to retain everything  
  
I'd suggest to just clone the identical docs and host the same content on `/libs/spirit/doc/`, `/libs/spirit_classic/doc/`, and `/libs/spirit_v2/doc/` (for now).  
  
Spirit's website has been obsolete for insane amount of time so it doesn't harm end-users even if we just end up showing same content. After the migration is complete I'll do the remaining work and update the docs accordingly. Just don't make the URLs dead.  
  
>  (2) every CMakeLists file linking to Boost::spirit will now be broken.  
  
Spirit's `CMakeLists.txt` is already broken. I can't manage it to work even if I use the one generated via `b2 install`. I also can't use it with `git submodule add` and `add_subdirectory`.   
  
Even if it appears to correctly dispatch `include/` with some intense `cmake -D....` patching, the current `CMakeLists.txt` does not contain any fundamental compile options and shall be considered technically broken.   
  
Believe me, I tried in many applications. It's on my todo list but you don't need to care about it during the migration process.  
  
---  
  
If I'm missing something please let me know.

---

## Comment 17

> Username: pdimov  
> Created at: 2025-09-10 15:57:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275576025  

> Spirit's `CMakeLists.txt` is already broken.  
  
I don't know what you consider broken, but e.g. Serialization uses Boost::spirit in its CMakeLists.txt file, and builds with CMake, so it's obviously not broken for everyone and every use.  
  
https://github.com/boostorg/serialization/blob/8a8c62864f05732131bf6785d54466d8ac6cd477/CMakeLists.txt#L85

---

## Comment 18

> Username: saki7  
> Created at: 2025-09-10 16:02:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275591041  

@pdimov Modern CMake applications expect libraries to set [`target_compile_features`](https://cmake.org/cmake/help/latest/command/target_compile_features.html) for naturally resolving minimal required C++ version. Spirit's `CMakeLists.txt` does not even have this level of support.   
  
Quoting my comment:  
  
> Even if it appears to correctly dispatch `include/` with some intense `cmake -D....` patching, the current `CMakeLists.txt` does not contain any fundamental compile options and shall be considered technically broken.  
  
A well-established build script that does not follow the best practices shall be considered broken.  
  
Let's stop talking about this. It's almost off-topic. We should focus on just doing the migration or we'll end up wasting another 4 months. (I'm not accusing you for this; I myself was lazy and didn't contact you recently. That is my bad.)

---

## Comment 19

> Username: pdimov  
> Created at: 2025-09-10 16:12:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275624403  

> A well-established build script that does not follow the best practices shall be considered broken.  
  
What best practices does it not follow?  
  
> Let's stop talking about this. It's almost off-topic.  
  
Well, no, let's hash this out. It's relevant for the split.

---

## Comment 20

> Username: saki7  
> Created at: 2025-09-10 16:12:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275626328  

> What best practices does it not follow?  
  
[target_compile_features](https://cmake.org/cmake/help/latest/command/target_compile_features.html)

---

## Comment 21

> Username: pdimov  
> Created at: 2025-09-10 16:17:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275643617  

The autogenerated CML gets the C++ level from meta/libraries.json, and since Spirit classic supports C++03, your meta/libraries.json specifies C++03:  
  
https://github.com/boostorg/spirit/blob/70f968ef6cda4295d65421068457eef3b4308882/meta/libraries.json#L18  
  
If you put something else there, `boostdep --cmake` will add `target_compile_features` with the appropriate value.

---

## Comment 22

> Username: saki7  
> Created at: 2025-09-10 16:24:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275667608  

A "modular CMake system" intuitively suggests that the library is usable with `git submodule add` and `add_subdirectory`.  
  
For Spirit's case, it's: `git submodule add https://github.com/boostorg/boost.git && git submodule update --init --recursive` and `add_subdirectory(boost/libs/spirit)`. I expect it to work out-of-the-box because Spirit is header-only in the first place.  
  
Even if a CMake project requires additional configuration to work, the "best practices" is to define a custom target that runs such commands automatically.  
  
Is Boost's "modular CMake system" (lol) configured to run such commands?   
No.  
  
I consider it broken. I don't care how others think. This is my personal feeling, as my full life experience of being a developer. I personally consider it broken, and I plan to fix it soon, as a maintainer. I don't care how you argue because you're saying "it's just working in my environment!" and that logically does not mean "it's well-configured based on the best practices". Period.  
  
Can you stop complaining about my decisions in the first place?  
  
If you can't agree with my opinions then we simply can't collaborate.  
  
Are we migrating the repositories or are we continuing to do this internet battle? Can we collaborate? Thanks!

---

## Comment 23

> Username: saki7  
> Created at: 2025-09-10 16:36:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275702728  

@apolukhin This is how I gave up the constructive discussion with Peter 4 months ago. What do you think?

---

## Comment 24

> Username: pdimov  
> Created at: 2025-09-10 17:01:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275768122  

> Can you stop complaining about my decisions in the first place?  
  
I only care about your decisions when I'm forced to. I would like nothing more than not having to care about them at all.  
  
When you're maintaining a Boost library you are part of a community, and your decisions affect others within it. For example, you can't put whatever you like in your CMakeLists.txt file, as this may break the Boost CMake build, or installation (as two other maintainers recently discovered.) When you up your requirement from C++17 to C++23, C++17 libraries that depend on you are now broken. When we remove the Boost::spirit CMake target, people whose code depends on it will break. And so on.  
  
This is not something that is specific to you; we're all constrained by our existing users and the existing projects that depend on our libraries.  
  
This aside, let's get back to our renaming. Current consensus seems to be `libs/spirit` becomes latest and greatest (x3 for now), and we make two other repos `spirit_classic` and `spirit_v2`?  
  
If I make two empty repos with these names, will that be enough for you to begin work? Or would you prefer I duplicate `boostorg/spirit` using this procedure: https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository?r603

---

## Comment 25

> Username: apolukhin  
> Created at: 2025-09-10 17:03:30 UTC  
> Updated at: 2025-09-10 17:04:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275774996  

I'd rather put the CMake discussion into a mailing list and concentrate here on a working solution for separation and directory names bikeshedding.  
  
I have my own concerns with current CMake status, but the concerns are mostly related to testing. I have neither an idea how to make it better nor a solid thought on what is causing pain. Give me a few days to express the problem in mailing list...  
  
There are some issues with CMake, @pdimov puts an insane amount of effort to make it better. We can not solve everyting in this issue, so let's concentrate on less global things  
  
>   
    boostorg/spirit -- keep, X3  
    boostorg/spirit_classic -- new repo, Classic  
    boostorg/spirit_v2 -- new repo, Qi & Karma & Lex (& /repository/)  
  
spirit_v2 name gives an impression that it is more modern than spirit. spirit, spirit_v2 and spirit_x3 seem better

---

## Comment 26

> Username: saki7  
> Created at: 2025-09-10 17:37:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275910792  

> This aside, let's get back to our renaming. Current consensus seems to be libs/spirit becomes latest and greatest (x3 for now),   
  
My original intent was that we could imply that X3 is the _default_ version, by choosing the simplest "spirit" identifier for X3. However, regarding Antony's comment, I realized that the "_v2" suffix contradicts with this implication.  
  
How about this?  
  
- `boostorg/spirit_x3` -- renamed from `boostorg/spirit`  
- `boostorg/spirit_classic` -- new repo  
- `boostorg/spirit_v2` -- new repo  
  
I think the only technical concern regarding the absence of the plain "spirit" is that existing systems, including user-facing ones, will going to point to the now-empty "spirit" repository.  
  
However,   
  
- We can additionally create a symlink: `/boost/libs/spirit` -> `/boost/libs/spirit_x3` (Note: this is file path on `boostorg/boost`)  
- GitHub will automatically redirect Git requests for `boostorg/spirit` to `boostorg/spirit_x3`, as long as we use the GitHub's web UI to rename the repo  
- Someone with privileges can configure our HTTP server to redirect `/libs/spirit/doc/` to `/libs/spirit_x3/doc/` (Note: this is URL)  
  - I think this resolves [Peter's concern (1)](https://github.com/boostorg/spirit/issues/795#issuecomment-3275439199)  
  
... so the absence of "spirit" would not affect the end users.  
Please correct me if I'm wrong. If not, I think we can choose the above scheme.  
  
---  
  
> If I make two empty repos with these names, will that be enough for you to begin work? Or would you prefer I duplicate `boostorg/spirit` using this procedure: https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository?r603  
  
I'd like to start working on the 2 empty repositories.  
  
I think that special command you mentioned (`git push --mirror`) generates the special "mirrored from" repository, e.g. <https://github.com/gcc-mirror/gcc/>, so it's not necessary for our case.

---

## Comment 27

> Username: pdimov  
> Created at: 2025-09-10 17:45:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3275940142  

I suspect that we'll have fewer problems if we retain libs/spirit (and boostorg/spirit) to be classic. It's not ideal from discoverability perspective, but if we put a large and prominent notice at the top of the classic documentation pointing to V2 and X3, that would probably be enough to direct users towards the later libraries.  
  
This might also be a good opportunity to consider retaining x3 as is/was (C++17) and make x4 which is the C++23 one - we're paying the splitting costs anyway so now's the time to add one more repo.

---

## Comment 28

> Username: saki7  
> Created at: 2025-09-10 18:21:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3276048924  

@djowel do you feel okay if the name "spirit" will going to point to the now obsolete library Classic? To be honest I feel like below two schemes are almost equally viable and I'd like to hear your opinion.  
  
saki7's idea:  
  
- `boostorg/spirit_x3` -- renamed from boostorg/spirit. GitHub and web server handles redirects  
- `boostorg/spirit_classic` -- new repo  
- `boostorg/spirit_v2` -- new repo  
  
pdimov's idea (please correct me if I'm wrong):  
  
- `boostorg/spirit` -- keep repo; all files except for Classic ones are deleted. Big text on docs guide users to the other repos  
- `boostorg/spirit_x3` -- new repo  
- `boostorg/spirit_v2` -- new repo

---

## Comment 29

> Username: saki7  
> Created at: 2025-09-10 18:33:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3276087071  

To be fair, I'm going to accept Joel's suggestion.

---

## Comment 30

> Username: djowel  
> Created at: 2025-09-10 21:22:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3276591805  

I'm with Peter here, I think. It offers the least disruption, especially for dependent libraries like serialization, which uses 'classic'. We can just have a big, bold message of this fact and link to the latest X3 library.

---

## Comment 31

> Username: djowel  
> Created at: 2025-09-10 21:28:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3276607807  

> This might also be a good opportunity to consider retaining x3 as is/was (C++17) and make x4 which is the C++23 one - we're paying the splitting costs anyway so now's the time to add one more repo.  
  
Sounds like a good idea, but this will ultimately present another maintenance burden that we'd probably want to avoid.

---

## Comment 32

> Username: vinniefalco  
> Created at: 2025-09-11 14:28:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3281075618  

> * Someone with privileges can configure our HTTP server to redirect `/libs/spirit/doc/` to `/libs/spirit_x3/doc/` (Note: this is URL)  
  
No, we rather not do that. Having a uniform set of server rules makes it easier to maintain and scale the website.

---

## Comment 33

> Username: Flamefire  
> Created at: 2025-09-11 14:41:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3281148896  

> A "modular CMake system" intuitively suggests that the library is usable with `git submodule add` and `add_subdirectory`.  
>   
> For Spirit's case, it's: `git submodule add https://github.com/boostorg/boost.git && git submodule update --init --recursive` and `add_subdirectory(boost/libs/spirit)`. I expect it to work out-of-the-box because Spirit is header-only in the first place.  
  
The library is not standalone. So you'd need `add_subdirectory` for all dependencies too as with any other source-build. We test such things in some libraries.     
Alternatively you can `add_subdirectory(boost)` and use `BOOST_INCLUDE_LIBRARIES=spirit` to have more auto-magic in adding dependencies. This works and gets regularly tested.  
  
>   
> Even if a CMake project requires additional configuration to work, the "best practices" is to define a custom target that runs such commands automatically.  
>   
> Is Boost's "modular CMake system" (lol) configured to run such commands? No.  
  
  
So no "commands" are required. It is on you to make sure all dependencies are available.  
  
  
> ... so the absence of "spirit" would not affect the end users.  
  
What about users doing `find_package(Boost COMPONENTS spirit)` && `link...(Boost::spirit)`?  
  
  
  
> > This might also be a good opportunity to consider retaining x3 as is/was (C++17) and make x4 which is the C++23 one - we're paying the splitting costs anyway so now's the time to add one more repo.  
>   
> Sounds like a good idea, but this will ultimately present another maintenance burden that we'd probably want to avoid.  
  
To me this sounds the better solution. We have e.g. `variant` and `variant2` already, so we could have `spirit` and `spirit4`.     
Current users can keep using the (mixed) C++03/c++17 variant `Boost::spirit` (which doesn't get much updates if at all) while `Boost::spirit4` would only contain the "new stuff" and correctly set the required C++ standard to 23. This seems to be a goal too:  
  
> Modern CMake applications expect libraries to set [target_compile_features](https://cmake.org/cmake/help/latest/command/target_compile_features.html) for naturally resolving minimal required C++ version. Spirit's CMakeLists.txt does not even have this level of support.  
  
Currently there is no need/possibility for this: The lowest C++ version you could set is 98 which any supported compiler supports. So there is no need to explicitly set it. And setting a higher version will break users only using the classic variant(s)  
  
Hence the split, or an extra target at least, has benefits there: It can correctly advertise the requirements. Maybe in the process the include-path could be cleaned up to e.g. `#include <boost/spirit4/binary.hpp>` instead of `#include <boost/spirit/home/x3/binary.hpp>`

---

## Comment 34

> Username: saki7  
> Created at: 2025-09-11 21:58:32 UTC  
> Updated at: 2025-09-11 21:59:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3282759707  

We already have a strong consensus on splitting Spirit into 3 repositories. https://github.com/boostorg/spirit/issues/795#issuecomment-3276591805  
  
The next step should be start doing the actual work. I initially said that  
I can do the work if Peter gives me permission on 2 empty repositories.  
However I now think the work is both technically and politically impossible  
for me to accomplish, because an unexpected amount of people is   
accusing me on the recent breaking changes, which I initially intended for   
a good reason.  
  
I think the community does not trust me for doing such important work.  
  
I need some core developer's support on the process.   
Can you help me? @pdimov @djowel   
  
Right now I am so afraid that I can't convince myself on pushing a single   
Git commit for this migration process.

---

## Comment 35

> Username: cmazakas  
> Created at: 2025-09-11 22:22:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3282816571  

> However I now think the work is both technically and politically impossible  
> for me to accomplish, because an unexpected amount of people is  
> accusing me on the recent breaking changes, which I initially intended for  
> a good reason.  
  
Hey, don't let that bother you.  
  
Boost failed you on that, and we must own that.  
  
Passions run high, as is the case for any technically nuanced and difficult project.  
  
We failed you because we should've just told you to add a `BOOST_PRAGMA_MESSAGE("hey, this is going to be C++23 in 2 Boost releases")` and then ended the conversation there, but that's not what happened.  
  
I think the zeal and passion you bring back to Boost and Spirit are overwhelmingly welcomed, at least by people like me. Don't get discouraged.

---

## Comment 36

> Username: djowel  
> Created at: 2025-09-12 00:29:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3283070018  

> > However I now think the work is both technically and politically impossible  
> > for me to accomplish, because an unexpected amount of people is  
> > accusing me on the recent breaking changes, which I initially intended for  
> > a good reason.  
>   
> Hey, don't let that bother you.  
>   
> Boost failed you on that, and we must own that.  
>   
> Passions run high, as is the case for any technically nuanced and difficult project.  
>   
> We failed you because we should've just told you to add a `BOOST_PRAGMA_MESSAGE("hey, this is going to be C++23 in 2 Boost releases")` and then ended the conversation there, but that's not what happened.  
>   
> I think the zeal and passion you bring back to Boost and Spirit are overwhelmingly welcomed, at least by people like me. Don't get discouraged.  
  
I trust you @saki7. Do not be discouraged. What you are doing is VERY important.

---

## Comment 37

> Username: saki7  
> Created at: 2025-09-12 23:08:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3287067959  

Thank you for your encouragements. I decided that starting Spirit.X4 would be the best choice.  
Since the movement is involving organization-wide consensus anyway, I posted my decision on the mailing list.  
  
https://lists.boost.org/archives/list/boost@lists.boost.org/message/6MKZLOGQXMBDKSBGY3GYNCSIC3DGHWQP/

---

## Comment 38

> Username: saki7  
> Created at: 2025-09-13 01:13:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3287322956  

Superseded by boostorg/spirit_x4#2

---

## Comment 39

> Username: jefftrull  
> Created at: 2025-10-30 19:06:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/795#issuecomment-3469653727  

@saki7 if it is decided to move Spirit Classic, I hope someone will file an issue on Wave to make appropriate updates. Thanks!
