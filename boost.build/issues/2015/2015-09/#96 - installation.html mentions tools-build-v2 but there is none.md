# #96 - installation.html mentions tools/build/v2 but there is none. [Closed]

> Username: cppljevans  
> Created at: 2015-09-17 17:18:11 UTC  
> Updated at: 2015-09-25 06:46:17 UTC  
> Closed at: 2015-09-25 06:46:17 UTC  
> Url: https://github.com/boostorg/build/issues/96  

http://www.boost.org/build/doc/html/bbv2/installation.html says:  
  
If you are not using a Boost.Build package, but rather the version bundled with the Boost C++ Libraries, the above commands should be run in the tools/build/v2 directory.  
  
However, my boost_1_59_0/tools/build directory has no v2 subdirectory; however,  
it does have the bootstrap.sh file mentioned earlier.  I'm guessing that is a typo  
and should read:  
  
If you are not using a Boost.Build package, but rather the version bundled with the Boost C++ Libraries, the above commands should be run in the tools/build directory.

---

## Comment 1

> Username: vprus  
> Created at: 2015-09-25 06:46:17 UTC  
> Url: https://github.com/boostorg/build/issues/96#issuecomment-143143728  

Thanks for the report! Fixed in a7635db1c826f567afef27053ee58948b6a94723 along with 3 other places in documentation.
