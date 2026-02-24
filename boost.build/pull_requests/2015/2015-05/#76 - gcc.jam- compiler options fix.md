# #76 gcc.jam: compiler options fix [Closed]

> Username: joerg-krause  
> Created at: 2015-05-02 12:29:26 UTC  
> Updated at: 2021-10-02 22:20:28 UTC  
> Closed at: 2015-05-04 14:38:05 UTC  
> Url: https://github.com/boostorg/build/pull/76  

Only PowerPC, SPARC, and x86 do support the -m32 and -m64 compiler options [1].  
  
Rather then excluding all architectures not supporting these options as it is  
done in commit c0634341d9ee2c02d3a55c91dafb988afc066c49, include all architectures that do support them.  
  
This will fix building Boost for the SuperH architecture with Buildroot [2].  
  
[1] https://gcc.gnu.org/onlinedocs/gcc/Option-Summary.html  
[2] http://autobuild.buildroot.net/results/ccd/ccd5c83963032ba49b1627b1dff39e34a9486943/build-end.log

---

## Comment 1

> Username: vprus  
> Created_at: 2015-05-04 14:38:05 UTC  
> Url: https://github.com/boostorg/build/pull/76#issuecomment-98731980  

Thanks, I've cherry-picked this change. I've edited to summary line of the commit message to a bit more specific.

---
