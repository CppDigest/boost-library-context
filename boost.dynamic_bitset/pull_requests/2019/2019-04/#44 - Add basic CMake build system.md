# #44 Add basic CMake build system [Merged]

> Username: samcday  
> Created at: 2019-04-28 15:16:25 UTC  
> Updated at: 2019-06-06 15:37:48 UTC  
> Merged at: 2019-06-06 15:37:48 UTC  
> Closed at: 2019-06-06 15:37:48 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-04-28 22:52:03 UTC  
> Updated_at: 2019-04-29 20:20:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487422290  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44?src=pr&el=h1) Report  
> Merging [#44](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/3f650f50c351957f9fb63c5035e1676505a42839?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #44   +/-   ##  
========================================  
  Coverage    79.05%   79.05%             
========================================  
  Files            5        5             
  Lines          616      616             
  Branches       204      204             
========================================  
  Hits           487      487             
  Misses          24       24             
  Partials       105      105  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44?src=pr&el=footer). Last update [3f650f5...f40f975](https://codecov.io/gh/boostorg/dynamic_bitset/pull/44?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Changes requested]

> Username: glenfe  
> Created_at: 2019-04-28 22:55:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#pullrequestreview-231490051  

The dependency list should be: assert, config, core, integer, move, serialization, static_assert, throw_exception

---

## Comment 3

> Username: jeking3  
> Created_at: 2019-04-28 23:11:00 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487423402  

Given the discussion on the mailing list are these make changes still  
warranted?  
  
On Sun, Apr 28, 2019, 6:55 PM Glen Fernandes <notifications@github.com>  
wrote:  
  
> *@glenfe* requested changes on this pull request.  
>  
> The dependency list should be: assert, config, core, integer, move,  
> serialization, static_assert, throw_exception  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/dynamic_bitset/pull/44#pullrequestreview-231490051>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACZZ23JJ3YXYD2MGUGUFEPDPSYTO5ANCNFSM4HI6XCYQ>  
> .  
>

---

## Comment 4

> Username: samcday  
> Created_at: 2019-04-29 05:50:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487457916  

@jeking3 I'm not plugged into the Boost development community so I'm not sure what discussion you're referring to (I also can't seem to find a working link to boost dev mailing list archives!). All I know is I saw @Mike-Dev getting these basic CMake build scripts merged into a bunch of core boost modules as recently as a couple of weeks ago, so I figured I'd help out ;)

---

## Comment 5

> Username: samcday  
> Created_at: 2019-04-29 05:56:37 UTC  
> Updated_at: 2019-04-29 05:56:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487458649  

@glenfe thanks for the revised list! It's interesting that `boost/detail/workaround.hpp` comes from boost-config and not boost-detail. Guess I have a whole bunch of other PRs to revise!  
  
From what I can see, the serialization integration is not referenced from the core headers, and is thus optional. Given that boost-serialization is a hefty dependency, I wonder if it's worth omitting the hard requirement on it?

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2019-04-30 12:07:35 UTC  
> Updated_at: 2019-04-30 12:36:14 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487926504  

@samcday: Many thanks for the help. Due to some deadlines at work I was rather inactive on this over the last weeks. You can use boostdep from peter pdimov (https://github.com/boostorg/boostdep) to determine the list of dependencies.  
  
@jeking3: This is largely orthogonal to the work peter is doing and what was discussed on the ML. His work is about discovery of a boost distribution that has been compiled and installed via the "classic" b2 mechanism.   
  
Those cmake files here are about being able to build/use boost libraries from cmake via `add_subdirectory`.

---

## Comment 7

> Username: glenfe  
> Created_at: 2019-04-30 12:19:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487929859  

> From what I can see, the serialization integration is not referenced from the core headers, and is thus optional. Given that boost-serialization is a hefty dependency, I wonder if it's worth omitting the hard requirement on it?  
  
As long as there is a header in here that depends on Serialization, then Serialization should be included in any list of dependencies. To remove it, should require removing the dependency of DynamicBuffer on Serialization. (e.g. If not by removing the functionality, then by creation of a new Boost repository module, such as boostorg/dynamic_buffer_serialization, or by implementing the serialization support in such a way that there isn't a need to include any headers from boost/serialization).

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2019-04-30 12:35:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-487934126  

@glenfe : Anyone using that header should be depending on serialization directly anyway, so I think it isn't really necessary - but well this has been discussed to death on the ML.  
  
Anyway: What about providing two cmake targets e.g.  `Boost::dynamic_bitset_core` (not depending on serialization) and  `Boost::dynamic_bitset_with_integration` (that has that dependency) and let `Boost::dynamic_bitset` refer to one of those two (question of course is then to which one).

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-05-01 16:25:21 UTC  
> Updated_at: 2019-05-01 16:27:02 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-488332048  

Serialization is supposed to be optional (I would need to look at the headers however to make sure this is a true statement); if you use it, you depend on it.  It should not always be a dependency.  The safe approach however would be to have the default target be the most inclusive of dependencies, and people can reasonably choose to exclude optional dependencies if they are not using them.  
  
There is already a boost core, so dynamic_bitset_core may not be the best name.  Consider perhaps:  
  
dynamic_bitset_minimal  
dynamic_bitset_serialization  
... others ...  
dynamic_bitset_all == minimal plus all intermediate dependency targets

---

## Comment 10

> Username: glenfe  
> Created_at: 2019-05-01 16:37:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-488335771  

The default target should be what boostdep reports as dependencies of DynamicBitset. For the obvious reason, boostdep's list of dependencies for DynamicBitset includes Serialization. The default target appears to be `boost_dynamic_bitset`. The maintenance of the list of dependencies in this default target should be no more effort than using what boostdep outputs.

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2019-05-01 17:20:31 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-488348220  

But - as far as I can tell - projects that include `<boost/dynamic_bitset.hpp>` or `<boost/dynamic_bitset/dynamic_bitset.hpp>` don't get `<boost/dynamic_bitset/serialization.hpp>` anyway and hence don't depend on serialization. On the other hand: In order to use the stuff in `<boost/dynamic_bitset/serialization.hpp>` one has to include other parts of serialization and hence should specify the dependency separately.   
  
I just can't imagine a scenario, where adding serialization to the dependencies of dynamic_bitset would do any good. On the other hand, not adding the serialization dependency makes it much more usable (and easier to manage) because it doesn't end up in the dependency cycle.  
  
I should also add  that there doesn't exist a cmake file that provides a compatible `Boost::serialization` in the first place.  
  
I can understand the desire to just copy the output of boostdep , but I think in this particular case (and probably a few other cases where libs support serialization), it is counterproductive.

---

## Comment 12

> Username: glenfe  
> Created_at: 2019-05-01 23:33:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-488492457  

> I should also add that there doesn't exist a cmake file that provides a compatible Boost::serialization in the first place.  
  
That's fine. We're in no rush for having this CMakeLists.txt in DynamicBitset.   
  
That said, I am looking into a way to sever the repository/submodule dependency (yes, even "optional" as it is) of DynamicBitset on Serialization, while retaining the functionality. That would also address my concern.

---

## Comment 13

> Username: Mike-Devel  
> Created_at: 2019-05-02 06:44:31 UTC  
> Updated_at: 2019-05-02 11:52:24 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/44#issuecomment-488567970  

Well, if you are going to sever that connection anyway,  isn't that ist another reason, why omitting serialization would be appropriate?   
  
Note: Neither this, nor the fact that serialization doesn't have an appropriate cmake file yet are the reasons why I think serialization would not be part of the interface dependencies presented by the cmake target - they are just collaborating factors. I really think that Boost::serialization doesn't qualify as a dependency of the Boost::dynamic_bitset cmake target (It should be different if it was a compiled library).  
  
Anyway I think they're is nothing new I can say. In the end it is up to you library maintainers to decide what you want in your repositories.   
  
EDIT: I'll be happy either way.

---
