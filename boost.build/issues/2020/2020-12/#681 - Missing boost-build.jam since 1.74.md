# #681 - Missing boost-build.jam since 1.74 [Closed]

> Username: filips123  
> Created at: 2020-12-07 21:44:26 UTC  
> Updated at: 2020-12-22 19:47:59 UTC  
> Closed at: 2020-12-22 19:47:59 UTC  
> Url: https://github.com/boostorg/build/issues/681  

I have a problem that started occurring on Windows and Ubuntu since Boost 1.74 (B2 4.3-git). Boost and Boost Build were installed as specified in the [user manual](https://boostorg.github.io/build/manual/master/index.html) (first bootstrap, then b2 install). I also installed Boost 1.73 and 1.74 side by side in the exact same way, and 1.73 works but 1.74 doesn't.  
  
When I try to build any project, I get this error:  
  
```  
Unable to load B2: could not find 'boost-build.jam'  
---------------------------------------------------  
Attempted search from '/tmp/boost-test' up to the root at '/usr/local/bin/b2'  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
```  
  
I compared installation directories of both versions. In 1.73 installation, I have `C:\Boost\share\boost-build` (Windows) and `/usr/local/share/boost-build` (Ubuntu) which contains `boost-build.jam` file and `src` directory. However, 1.74 installation only contains `src` directory, without `boost-build.jam`. The content of `boost-build.jam` is `boost-build src/kernel ;` (and license comments) and structures of both `src` dirs look the same at first glance.  
  
I think this is what is causing the problem, because when I manually copy `boost-build.jam` from 1.73 to 1.74 directory, build will work correctly. Is it intentional that system `boost-build.jam` now needs to be created manually by users? If yes, it should probably be specified in the user manual. If not, maybe file got somehow missed during updates.  
  
Also, I've noticed that the user manual says this in 4.3.0 changelog:  
  
> Add portable B2 system install option. This allows the b2 executable and the build system files to live side by side. And hence to be (re)located anywhere on disk. Soon to be used to supports Windows and other installers. This removes the need for the boost-build.jam file for bootstrap. Making it easier for users to get started.  
  
Could this be the cause of the problem? If so, how to activate "portable B2 system install option" so B2 doesn't need `boost-build.jam`?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-22 19:47:59 UTC  
> Url: https://github.com/boostorg/build/issues/681#issuecomment-749743202  

This is fixed in develop, it was a duo of issues. And will be in the next release of B2 (very soon). And hence will be in the 1.75 Boost release.
