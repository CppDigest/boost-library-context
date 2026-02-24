# #249 - clang-freebsd support [Open]

> Username: fasxmut  
> Created at: 2017-10-05 05:20:32 UTC  
> Updated at: 2021-06-26 03:10:26 UTC  
> Url: https://github.com/boostorg/build/issues/249  

Successfully built boost.build on my freebsd system.  I found it displays clang-linux or clang-gnu-linux commands and dirs. Can you help to make it detect the real system to display or create the right arch info? including some other BSDes.  
bjam is really useful. Thank you.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-09 15:35:46 UTC  
> Url: https://github.com/boostorg/build/issues/249#issuecomment-356319357  

I think the right thing to do is not to split clang into separate toolsets by platform in the first place.  Except for clang-win, which is a totally different beast and should probably be called clang-cl, there's not much difference between platforms beyond what inheritance from gcc already handles.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:47 UTC  
> Url: https://github.com/boostorg/build/issues/249#issuecomment-868936541  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
