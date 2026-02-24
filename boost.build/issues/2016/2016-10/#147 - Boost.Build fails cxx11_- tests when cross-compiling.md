# #147 - Boost.Build fails cxx11_* tests when cross-compiling. [Closed]

> Username: ClaymorePT  
> Created at: 2016-10-29 00:48:53 UTC  
> Updated at: 2018-01-10 00:50:24 UTC  
> Closed at: 2018-01-10 00:50:24 UTC  
> Url: https://github.com/boostorg/build/issues/147  

If I try to cross-compile Boost.Fiber for the armv6 architecture, the cxx11_\* requirements in the Jamfile.v2 will fail, because the building system will try to execute the binaries on the host.  
  
This should not happen.  
The fact that the binaries are compiled should be enough to guarantee the cxx11_\* support by the compiler.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-09 23:38:45 UTC  
> Url: https://github.com/boostorg/build/issues/147#issuecomment-356450085  

This should go to Boost.Config, as that is where these tests live.

---

## Comment 2

> Username: ClaymorePT  
> Created at: 2018-01-10 00:49:42 UTC  
> Url: https://github.com/boostorg/build/issues/147#issuecomment-356462933  

@swatanabe   
Better late than never 👍 :)
