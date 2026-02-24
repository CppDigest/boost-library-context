# #545 - RUNPATH not added to shared libraries with dependencies [Open]

> Username: jefftrull  
> Created at: 2020-03-05 18:39:58 UTC  
> Updated at: 2021-05-29 18:22:28 UTC  
> Url: https://github.com/boostorg/build/issues/545  

I'm trying to eliminate the deprecated Timer API usage from Boost.Wave. The new API seems to required linking to the Timer library (i.e. is no longer header-only). I added `/boost//timer` to the Wave tool dependencies above [this line](https://github.com/boostorg/wave/blob/785dfd0eeed06ec5854dd51cbbbc1efb9bab4983/tool/build/Jamfile.v2#L44).  
  
Now, when I run the standalone Wave tool I get the following error message:  
`error while loading shared libraries: libboost_chrono.so.1.73.0: cannot open shared object file: No such file or directory`  
  
After some investigation it appears that while the wave executable has a `RUNPATH` set, `libboost_timer.so.1.73.0` does not, and it depends on Chrono. The wave executable's `RUNPATH` does include the location of `libboost_chrono.so.1.73.0` - but this is apparently not enough.  
  
IMO bjam should be adding a RUNPATH to `libboost_timer.so.1.73.0` for its Chrono dependency. When I do this manually everything works.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-03-05 20:25:46 UTC  
> Url: https://github.com/boostorg/build/issues/545#issuecomment-595430042  

Or, alternatively, add RPATH instead of RUNPATH to the executable, because it's inherited by dependencies. I'm not sure how this would translate to a linker option though.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-03-05 22:08:45 UTC  
> Url: https://github.com/boostorg/build/issues/545#issuecomment-595472269  

FWIW CMake adds RUNPATH to shared libs with dependencies

---

## Comment 3

> Username: pdimov  
> Created at: 2020-03-05 23:20:58 UTC  
> Url: https://github.com/boostorg/build/issues/545#issuecomment-595494918  

The relevant portion is here, I suppose: https://github.com/boostorg/build/blob/cf9933462a2a942bd1b95b839c1b2e427201fe8f/src/tools/generators/linking-generator.jam#L64-L73

---

## Comment 4

> Username: pdimov  
> Created at: 2020-03-07 17:11:42 UTC  
> Updated at: 2020-03-07 17:12:23 UTC  
> Url: https://github.com/boostorg/build/issues/545#issuecomment-596109585  

Further information: b2 uses `-rpath`, which by default sets DT_RPATH. However, Debian have decided to patch their `ld` to use `--enable-new-dtags` by default, which sets DT_RUNPATH instead. If we want to revert to the old behavior on Debian, we need to pass `-Wl,--disable-new-dtags`.  
  
Reference: https://stackoverflow.com/questions/52018092/how-to-set-rpath-and-runpath-with-gcc-ld

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:58 UTC  
> Url: https://github.com/boostorg/build/issues/545#issuecomment-850877301  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
