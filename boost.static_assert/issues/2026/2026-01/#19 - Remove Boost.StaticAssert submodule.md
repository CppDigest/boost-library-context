# #19 - Remove Boost.StaticAssert submodule [Open]

> Username: Lastique  
> Created at: 2026-01-22 20:38:02 UTC  
> Updated at: 2026-02-06 21:14:52 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19  

Since Boost.StaticAssert has been [merged](https://github.com/boostorg/config/pull/531) into Boost.Config, this submodule can be removed once all Boost libraries have removed references to it. For tracking purposes, here is the list of the relevant PRs:  
  
- https://github.com/boostorg/accumulators/pull/67  
- https://github.com/boostorg/algorithm/pull/130  
- https://github.com/boostorg/align/pull/24  
- https://github.com/boostorg/assert/pull/44  
- https://github.com/boostorg/assign/pull/50  
- https://github.com/boostorg/array/pull/30  
- https://github.com/boostorg/beast/pull/3074  
- https://github.com/boostorg/bimap/pull/51  
- https://github.com/boostorg/charconv/pull/287  
- https://github.com/boostorg/chrono/pull/81  
- https://github.com/boostorg/circular_buffer/pull/54  
- https://github.com/boostorg/cobalt/pull/262  
- https://github.com/boostorg/compute/pull/898  
- https://github.com/boostorg/concept_check/pull/47  
- https://github.com/boostorg/config/pull/533  
- https://github.com/boostorg/contract/pull/41  
- https://github.com/boostorg/date_time/pull/250  
- https://github.com/boostorg/endian/pull/60  
- https://github.com/boostorg/function/pull/57  
- https://github.com/boostorg/function_types/pull/19  
- https://github.com/boostorg/fusion/pull/279  
- https://github.com/boostorg/geometry/pull/1447  
- https://github.com/boostorg/gil/pull/786  
- https://github.com/boostorg/graph/pull/450  
- https://github.com/boostorg/graph_parallel/pull/45  
- https://github.com/boostorg/hash2/pull/46  
- https://github.com/boostorg/heap/pull/57  
- https://github.com/boostorg/histogram/pull/419  
- https://github.com/boostorg/icl/pull/52  
- https://github.com/boostorg/interval/pull/48  
- https://github.com/boostorg/io/pull/17  
- https://github.com/boostorg/iostreams/pull/190  
- https://github.com/boostorg/lexical_cast/pull/95  
- https://github.com/boostorg/lockfree/pull/126  
- https://github.com/boostorg/math/pull/1351  
- https://github.com/boostorg/metaparse/pull/22  
- https://github.com/boostorg/move/pull/63  
- https://github.com/boostorg/mp11/pull/112  
- https://github.com/boostorg/mpi/pull/177  
- https://github.com/boostorg/mpl/pull/91  
- https://github.com/boostorg/mqtt5/pull/46  
- https://github.com/boostorg/multi_array/pull/42  
- https://github.com/boostorg/multi_index/pull/91  
- https://github.com/boostorg/mysql/pull/497  
- https://github.com/boostorg/odeint/pull/105  
- https://github.com/boostorg/pfr/pull/229  
- https://github.com/boostorg/phoenix/pull/125  
- https://github.com/boostorg/program_options/pull/155  
- https://github.com/boostorg/property_map/pull/36  
- https://github.com/boostorg/property_map_parallel/pull/4  
- https://github.com/boostorg/property_tree/pull/134  
- https://github.com/boostorg/proto/pull/39  
- https://github.com/boostorg/ptr_container/pull/44  
- https://github.com/boostorg/python/pull/509  
- https://github.com/boostorg/random/pull/152  
- https://github.com/boostorg/range/pull/162  
- https://github.com/boostorg/rational/pull/57  
- https://github.com/boostorg/redis/pull/386  
- https://github.com/boostorg/regex/pull/269  
- https://github.com/boostorg/serialization/pull/340  
- https://github.com/boostorg/signals2/pull/84  
- https://github.com/boostorg/smart_ptr/pull/122  
- https://github.com/boostorg/sort/pull/109  
- https://github.com/boostorg/spirit/pull/846  
- https://github.com/boostorg/stacktrace/pull/223  
- https://github.com/boostorg/statechart/pull/27  
- https://github.com/boostorg/system/pull/139  
- https://github.com/boostorg/test/pull/474  
- https://github.com/boostorg/thread/pull/417  
- https://github.com/boostorg/tuple/pull/32  
- https://github.com/boostorg/type_traits/pull/203  
- https://github.com/boostorg/typeof/pull/27  
- https://github.com/boostorg/ublas/pull/198  
- https://github.com/boostorg/units/pull/66  
- https://github.com/boostorg/unordered/pull/341  
- https://github.com/boostorg/uuid/pull/192  
- https://github.com/boostorg/variant/pull/115  
- https://github.com/boostorg/wave/pull/245  
- https://github.com/boostorg/xpressive/pull/29  
  
- https://github.com/boostorg/boostdep/pull/33  
- https://github.com/boostorg/cmake/pull/103  
- https://github.com/boostorg/boost-ci/pull/313  
  
Once these PRs are merged and propagate to master, I propose to add a release note for the next Boost release to notify users that the submodule is deprecated and will soon be removed. Then, after 3 Boost releases, actually remove the submodule.  
  
CC @pdimov

---

## Comment 1

> Username: pdimov  
> Created at: 2026-01-22 23:50:31 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3787404452  

No, I don't think we should remove it. This will break way too much stuff.

---

## Comment 2

> Username: Lastique  
> Created at: 2026-02-02 20:21:49 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3837327432  

@pdimov Could you please merge PRs for other libraries, at least the unmaintained and widely used ones? Assign, CircularBuffer, ConceptCheck, Config, Contract, Compute, MPL, PropertyMap, Proto, Range, TypeOf, Variant seem to be ready for merge, and other libraries may be enabled by these (especially, ConceptCheck and MPL).

---

## Comment 3

> Username: pdimov  
> Created at: 2026-02-03 00:11:29 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3837950359  

I've added you to the community maintenance team, which should give you access to some of these.

---

## Comment 4

> Username: Lastique  
> Created at: 2026-02-03 02:13:27 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3838491978  

Thanks. I've merged what I could, the rest are still out of my reach.

---

## Comment 5

> Username: pdimov  
> Created at: 2026-02-03 14:02:10 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3841514110  

You'll need to merge to master as well, because I'm getting https://github.com/boostorg/circular_buffer/actions/runs/21631032975/job/62343535609

---

## Comment 6

> Username: Lastique  
> Created at: 2026-02-03 15:08:14 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3841902628  

I've merged to master.

---

## Comment 7

> Username: Lastique  
> Created at: 2026-02-04 22:05:14 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3849981772  

Could you please merge PRs for Accumulators, Algorithm, Chrono, Contract, DateTime, FunctionTypes, MultiArray, PropertyTree, Thread, Variant, Xpressive? I'm not sure if Range and Serialization are actively maintained, so maybe those too.

---

## Comment 8

> Username: pdimov  
> Created at: 2026-02-04 23:53:27 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3850318623  

Variant should be actively maintained, but I'll handle the rest.

---

## Comment 9

> Username: pdimov  
> Created at: 2026-02-06 01:20:58 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3857294165  

I've merged Accumulators, Chrono, FunctionTypes, Xpressive. Algorithm seems maintained by @mclow.

---

## Comment 10

> Username: pdimov  
> Created at: 2026-02-06 01:31:46 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3857336623  

It takes longer than expected because I use this opportunity to fix or add CI.  
  
Contract, DateTime merged as well; CI seems up to date there.  
  
I've given you write access to MultiArray because it seems unmaintained and I see you have a pending PR there.

---

## Comment 11

> Username: pdimov  
> Created at: 2026-02-06 01:34:13 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3857346315  

Gave you access to PropertyTree as well.

---

## Comment 12

> Username: pdimov  
> Created at: 2026-02-06 01:36:10 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3857351402  

And Thread while we're at it.

---

## Comment 13

> Username: Lastique  
> Created at: 2026-02-06 12:56:39 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3860305294  

Thanks, I've merged MultiArray, PropertyTree and Thread. Is there anything else you think you could merge?

---

## Comment 14

> Username: pdimov  
> Created at: 2026-02-06 13:35:46 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3860511209  

The remaining ones from your list above are Variant, Range, Serialization, and all have active maintainers if I'm not mistaken.

---

## Comment 15

> Username: Lastique  
> Created at: 2026-02-06 15:49:55 UTC  
> Updated at: 2026-02-06 21:14:52 UTC  
> Url: https://github.com/boostorg/static_assert/issues/19#issuecomment-3861156071  

I listed the PRs for libraries that I considered low-level and not actively maintained. At this point, the unmerged PRs are:  
  
- https://github.com/boostorg/compute/pull/898 - no CI, and seems unmaintained. Latest commits are you merging PRs. Maybe you could handle it as well?  
- https://github.com/boostorg/geometry/pull/1447 - CI green. There are active maintainers, although not very responsive.  
- https://github.com/boostorg/graph/pull/450 - CI green. There seem to be active maintainers.  
- https://github.com/boostorg/graph_parallel/pull/45 - CI failures unrelated. Seems, you did some maintenance there recently.  
- https://github.com/boostorg/histogram/pull/419 - CI green, actively maintained, although not very responsive.  
- https://github.com/boostorg/icl/pull/52 - CI green, actively maintained.  
- https://github.com/boostorg/mpi/pull/177 - No CI, actively maintained, although not very responsive.  
- https://github.com/boostorg/mqtt5/pull/46 - CI failures seem unrelated, actively maintained.  
- https://github.com/boostorg/python/pull/509 - CI green, actively maintained.  
- https://github.com/boostorg/range/pull/162 - No CI. Latest commit is you merging a PR, and before that from Neil Groves and Rene Rivera from April 2025. There are 36 issues and 18 PRs, including mine, so not quite actively maintained.  
- https://github.com/boostorg/redis/pull/386 - CI is failing because of Range and Test, although there are also some unrelated errors. Actively maintained.  
- https://github.com/boostorg/regex/pull/269 - CI green, actively maintained, although not very responsive.  
- https://github.com/boostorg/serialization/pull/340 - CI failures unrelated. Latest commits are from Robert Ramey from August 2024, there are 60 issues and 11 PRs, so not quite actively maintained.  
- https://github.com/boostorg/stacktrace/pull/223 - CI green, actively maintained.  
- https://github.com/boostorg/test/pull/474 - CI is failing because of Range, actively maintained.  
- https://github.com/boostorg/ublas/pull/198 - CI is mostly non-functional, green where it runs. Latest commit is from you from 2023, so not actively maintained.  
- https://github.com/boostorg/units/pull/66 - No CI. Latest commit is from Rene Rivera in May 2025 and the next one is from 2021. There are 17 issues and 17 PRs. Not actively maintained.  
- https://github.com/boostorg/variant/pull/115 - CI green, actively maintained.  
- https://github.com/boostorg/wave/pull/245 - CI green where it runs, actively maintained, although not very responsive.  
- https://github.com/boostorg/boost-ci/pull/313 - CI is failing because Align hasn't been merged to master, actively maintained.  
  
[*] In the above, I'm not considering Drone CI as it is inaccessible in my region and right now I can't view it.  
  
If my analysis is correct, it is worth looking at Compute, GraphParallel, Range, Serialization, UBLAS, Units.
