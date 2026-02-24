# #596 Default MSVC to a native platform [Merged]

> Username: Kojoley  
> Created at: 2020-05-08 01:22:45 UTC  
> Updated at: 2021-10-02 20:59:18 UTC  
> Merged at: 2020-09-11 19:14:12 UTC  
> Closed at: 2020-09-11 19:14:13 UTC  
> Url: https://github.com/boostorg/build/pull/596  

This also removes ambiguous implicit address-model ARM/ARM64 values.  
  
Closes #128  
Closes #277

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-11 01:01:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/596#pullrequestreview-408803321  

Logic looks good.

📁 src/tools/msvc.jam

```diff
2038 |+ switch $(.default-cpu-arch)
2039 |+ {
2040 |+ case x86   : .default-cpu-arch = i386 ;
```

> Username: grafikrobot  
> Created_at: 2020-05-11 01:00:47 UTC  
> Updated_at: 2020-05-11 01:17:02 UTC  
> Url: https://github.com/boostorg/build/pull/596#discussion_r422728658  

Indent case statements.


---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2020-05-11 01:16:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/596#pullrequestreview-408805930  

📁 src/tools/msvc.jam

```diff
2028 |+     if ! $(.cpus-on-$(arch)[2-])
2029 |+     {
2030 |+         .cpu-arch-$(.cpus-on-$(arch)) += <architecture>$(arch)/<address-model> ;
```

> Username: Kojoley  
> Created_at: 2020-05-11 01:16:26 UTC  
> Updated_at: 2020-05-11 01:17:02 UTC  
> Url: https://github.com/boostorg/build/pull/596#discussion_r422731430  

Meh, I have a bug here, this will be added twice if it is a native platform, fixed and forcepushed.


---

## Comment 3

> Username: grafikrobot  
> Created_at: 2020-06-02 00:31:46 UTC  
> Url: https://github.com/boostorg/build/pull/596#issuecomment-637197997  

Just an update... I'm working to resolve the type warnings this generates. Just taking a bit.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2020-09-11 13:55:48 UTC  
> Url: https://github.com/boostorg/build/pull/596#issuecomment-691110049  

Finally got the engine to build clean for 32/64 address-model build. Thanks for being patient.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-09-11 14:04:35 UTC  
> Url: https://github.com/boostorg/build/pull/596#issuecomment-691114844  

I am puzzled why you have rejected the PR then...

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2020-09-11 14:17:19 UTC  
> Url: https://github.com/boostorg/build/pull/596#issuecomment-691121593  

Sorry.. I pressed the wrong button :-(

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2020-09-11 14:18:12 UTC  
> Url: https://github.com/boostorg/build/pull/596#issuecomment-691121995  

Anyway.. benefit of closing and reopening.. CI gets to run again :-)

---

## Comment 8

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:17 UTC  
> Url: https://github.com/boostorg/build/pull/596#issuecomment-932819616  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
