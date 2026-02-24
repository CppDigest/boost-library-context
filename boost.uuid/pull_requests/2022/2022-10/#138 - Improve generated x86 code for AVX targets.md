# #138 Improve generated x86 code for AVX targets [Merged]

> Username: Lastique  
> Created at: 2022-10-26 07:22:28 UTC  
> Updated at: 2023-04-15 20:11:21 UTC  
> Merged at: 2023-04-15 18:24:52 UTC  
> Closed at: 2023-04-15 18:24:52 UTC  
> Url: https://github.com/boostorg/uuid/pull/138  

Prefer `vmovdqu` to `vlddqu` on CPUs supporting AVX. `vlddqu` has one extra cycle latency on Skylake and later Intel CPUs and is not merged to the following instructions as a memory operand, which makes the code slightly larger. Legacy SSE3 `lddqu` is still preferred because it is faster on Prescott and the same as `movdqu` on AMD CPUs. It also doesn't affect code size because `movdqu` cannot be converted to a memory operand as memory operands are required to be aligned in SSE.  
  
Closes https://github.com/boostorg/uuid/issues/137.  
  
Also, re-format the test code for MSVC bug 981648, no functional changes.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-10-26 15:46:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1292249387  

@pdimov Peter, MSVC-14.0 fails with ICE in [this CI run](https://ci.appveyor.com/project/jeking3/uuid-gaamf/builds/45184501/job/2f3s59y8ofopqcds#L1648) in `is_contiguous_range` implementation. Could you take a look please?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-10-26 16:10:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1292281383  

I'll look into it.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-10-27 00:10:38 UTC  
> Updated_at: 2023-03-15 01:41:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1292795301  

## [Codecov](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#138](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (86b1ae5) into [develop](https://codecov.io/gh/boostorg/uuid/commit/9df4da92a0852a66fc3334d664d9cc575e1c8eed?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9df4da9) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 86b1ae5 differs from pull request most recent head c7e0a70. Consider uploading reports for the commit c7e0a70 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/138/graphs/tree.svg?width=650&height=150&src=pr&token=6falIr5npV&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #138   +/-   ##  
========================================  
  Coverage    83.92%   83.92%             
========================================  
  Files           15       15             
  Lines          678      678             
  Branches       156      156             
========================================  
  Hits           569      569             
  Misses          18       18             
  Partials        91       91             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/detail/uuid\_x86.ipp](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC91dWlkX3g4Ni5pcHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9df4da9...c7e0a70](https://codecov.io/gh/boostorg/uuid/pull/138?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-01-20 17:09:31 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1398682873  

Ping @jeking3.

---

## Comment 5

> Username: Lastique  
> Created_at: 2023-03-14 21:59:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1468897468  

Rebased and updated the code to prefer movdqu starting with SSE4.1. It doesn't matter on CPUs not supporting AVX, but it is possible that SSE4.1 code will run on a modern CPU that does prefer movdqu to lddqu.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-03-15 00:04:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469028052  

Why not just use `movdqu` everywhere?

---

## Comment 7

> Username: Lastique  
> Created_at: 2023-03-15 00:52:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469068518  

Because lddqu is better on NetBurst CPUs. And there's also a workaround for MSVC codegen bug.

---

## Comment 8

> Username: pdimov  
> Created_at: 2023-03-15 01:11:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469090421  

> NetBurst CPUs  
  
Really? Have you seen one recently (as in, in the last decade)? :-)  
  
I had a Thinkpad with a P4D I gave away, its battery lasted about half an hour.

---

## Comment 9

> Username: Lastique  
> Created_at: 2023-03-15 01:28:32 UTC  
> Updated_at: 2023-03-15 01:29:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469128210  

Well, I'm fine with dropping support for NetBurst CPUs, but as I said, there's also MSVC bug, so the code wouldn't get much simpler anyway.

---

## Comment 10

> Username: pdimov  
> Created_at: 2023-03-15 01:37:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469142872  

It's still a simplification even if we still pretend to care about VS 2008. Not many parts of Boost still work with it, because it's not tested. (I still test msvc-9.0 on Appveyor in old libraries as a matter of habit but that's more of an exception and is not going to last.)

---

## Comment 11

> Username: Lastique  
> Created_at: 2023-03-15 01:43:01 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469153349  

The bug is only fixed in VS2015; VS2013 and before are affected.  
  
Anyway, I've pushed a commit to use movdqu universally, except for the MSVC workaround.

---

## Comment 12

> Username: pdimov  
> Created_at: 2023-03-15 01:48:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469161106  

Yes but without the VS2008 path, it's a single ifdef over _ReadWriteBarrier.

---

## Comment 13

> Username: Lastique  
> Created_at: 2023-03-15 01:57:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1469176430  

Ok, done. I don't want to remove the VS2008 workaround yet.

---

## Comment 14

> Username: pdimov  
> Created_at: 2023-03-21 18:16:45 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1478379911  

Here's some discussion on lddqu vs movdqu, for reference: https://community.intel.com/t5/Intel-ISA-Extensions/LDDQU-vs-MOVDQU-guidelines/m-p/1178965

---

## Comment 15

> Username: Lastique  
> Created_at: 2023-03-21 19:59:37 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1478503885  

Yeah, I forgot about that discussion, thanks for digging it up. Although it didn't result in a definitive answer - Intel reps didn't comment. In the end I was left with the opinion I had when I started it - use `lddqu` up until AVX, use `vmovdqu` with AVX and later, as before AVX `lddqu` is not worse than `movdqu` and is sometimes better.

---

## Comment 16

> Username: Lastique  
> Created_at: 2023-03-21 20:03:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1478508946  

@pdimov Since apparently Boost.UUID is no longer actively maintained again, maybe you could merge this? The Codecov failure does not seem to be caused by this.

---

## Comment 17

> Username: pdimov  
> Created_at: 2023-03-21 20:05:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1478511745  

I was going to wait until after the release, but I can merge it now if you insist.

---

## Comment 18

> Username: Lastique  
> Created_at: 2023-03-21 20:06:58 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1478514572  

No, after the release is fine. Thanks.

---

## Comment 19

> Username: Lastique  
> Created_at: 2023-04-15 18:11:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/138#issuecomment-1509918093  

A gentle reminder about this PR.

---
