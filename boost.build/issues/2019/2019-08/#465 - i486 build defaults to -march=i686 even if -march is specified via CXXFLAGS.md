# #465 - i486 build defaults to -march=i686 even if -march is specified via CXXFLAGS [Open]

> Username: kwinsch  
> Created at: 2019-08-08 12:33:52 UTC  
> Updated at: 2021-05-29 18:22:44 UTC  
> Url: https://github.com/boostorg/build/issues/465  

I export the CFLAGS, CXXFLAGS and ASFLAGS via a conan profile, which includes some default CXXFLAGS, including the correct `-march`, `-mtune` and other optimizations for an intel Atom prozessor. Unfortunatly, the boost build system is defaulting to i686, even when `-march=silvermont -mtune=silvermont -mfpmath=sse` (Bay Trail Intel Atom) is specified. The reason seems to be in https://github.com/boostorg/build/blob/develop/src/tools/gcc.jam#L1190 . Unfortunatly the list is outdated and is not matching the current one, valid for gcc 8.3+.  
  
I suggest to default to the supplied -march, if specified via the CXXFLAGS and only default to i686, if the information is missing.

---

## Comment 1

> Username: SSE4  
> Created at: 2019-08-08 13:27:03 UTC  
> Url: https://github.com/boostorg/build/issues/465#issuecomment-519517408  

/cc @grafikrobot

---

## Comment 2

> Username: Lastique  
> Created at: 2019-10-05 20:58:40 UTC  
> Url: https://github.com/boostorg/build/issues/465#issuecomment-538688038  

FYI, you can select the target instruction set by adding `instruction-set=...` to `b2` command line.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:13 UTC  
> Url: https://github.com/boostorg/build/issues/465#issuecomment-850877367  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
