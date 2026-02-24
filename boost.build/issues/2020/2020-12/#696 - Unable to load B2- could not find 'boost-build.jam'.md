# #696 - Unable to load B2: could not find 'boost-build.jam' [Closed]

> Username: yurivict  
> Created at: 2020-12-29 00:07:18 UTC  
> Updated at: 2020-12-30 12:58:08 UTC  
> Closed at: 2020-12-30 12:58:08 UTC  
> Url: https://github.com/boostorg/build/issues/696  

On FreeBSD it installs 'boost-build.jam' into /usr/local/share/boost-build/src/kernel/boost-build.jam but then looks for it at /usr/local/share/boost-build/boost-build.jam:  
  
```  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e99d8,0x7fffffffd478,0)  
 35142 b2       NAMI  "/usr/ports/devel/boost_build/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e9a18,0x7fffffffd478,0)  
 35142 b2       NAMI  "/usr/ports/devel/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e9a50,0x7fffffffd478,0)  
 35142 b2       NAMI  "/usr/ports/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e9a80,0x7fffffffd478,0)  
 35142 b2       NAMI  "/usr/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e9aa8,0x7fffffffd478,0)  
 35142 b2       NAMI  "/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e9ad0,0x7fffffffd478,0)  
 35142 b2       NAMI  "/usr/local/bin/.b2/kernel/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
 35142 b2       CALL  fstatat(AT_FDCWD,0x8007e9b10,0x7fffffffd478,0)  
 35142 b2       NAMI  "/usr/local/share/boost-build/boost-build.jam"  
 35142 b2       RET   fstatat -1 errno 2 No such file or directory  
```  
  
It also looks in build directories for some reason. It should only look for it where it was installed.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-29 15:57:12 UTC  
> Url: https://github.com/boostorg/build/issues/696#issuecomment-752134014  

The first part of that does sound like a bug to me. But the second part is by design. As it allows individual projects to override the system provided core build system with their own custom version. Although that is something that is likely to change in a future version of B2.  
  
I'll investigate the former as it's likely a fallout from rewriting the bootstrap logic.

---

## Comment 2

> Username: yurivict  
> Created at: 2020-12-30 04:20:38 UTC  
> Url: https://github.com/boostorg/build/issues/696#issuecomment-752322357  

This prevents packaging of ```py-libtorrent-rasterbar-2.0``` because it is now the only package that requires ```b2``` to build.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-12-30 12:58:08 UTC  
> Url: https://github.com/boostorg/build/issues/696#issuecomment-752560826  

This was already fixed in https://github.com/boostorg/build/commit/b29603fa88a614835359168e70b5a44eae8e642c#diff-14e404a46c0bb3c94122aaaccb81fa879f66930564b865c892230c0b67bee328  
  
As a workaround for the released version of B2 you can set the `BOOST_BUILD_PATH` environment variable to the `/usr/local/share/boost-build/src/kernel` path before trying to build.
