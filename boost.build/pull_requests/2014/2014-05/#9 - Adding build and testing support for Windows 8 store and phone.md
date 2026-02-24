# #9 [winrt support] Adding build and testing support for Windows 8 store and phone [Closed]

> Username: stgates  
> Created at: 2014-05-01 00:54:56 UTC  
> Updated at: 2021-10-02 22:36:03 UTC  
> Closed at: 2014-05-27 22:03:01 UTC  
> Url: https://github.com/boostorg/build/pull/9  

These changes enable building and testing Boost libraries to target Windows 8 store and phone using Visual Studio 2012. This is part of the work mentioned on the Boost dev mailing list:  
  
http://boost.2283326.n4.nabble.com/winrt-support-Adding-support-for-Windows-8-store-phone-to-Boost-libraries-tc4661713.html  
  
The changes are confined to msvc.jam and testing.jam, plus a small utility function added to utility.jam.  
##### msvc.jam changes  
  
To support deciding which Windows API to target I added a new feature <windows-api> with possible values desktop, store, and phone. The default value is desktop, there by keeping the current behavior exactly as is. If store or phone are specified the appropriate WINAPI_FAMILY macro is defined and linker flags added. Most of the changes are due to the fact that for phone there is a different setup script, vcvarsphoneall.bat, in a different location than the other setup scripts. There are a few small changes for adding ARM support as well.  
##### testing.jam changes  
  
To handle running tests against WinRT in an automated fashion that integrates well into the Boost workflow of running tests as part of the build, if <windows-api> is set to store, the test and all its binary dependencies need to have the APPCONTAINER requirement stripped. This then allows execution outside of the sandboxed Windows store environment. To do this all dependent dlls are copied into the tests executable’s folder, if using <link>shared. This is necessary to avoid modifying the APPCONTAINER flag on the actual product binaries. Just let me know if this needs more explanation, I’ll be glad to go into more detail if necessary.   
  
I also added a new feature to turn on/off whether or not test binaries are actually executed after building <disable-test-execution>. By default this is set to off, the current behavior. This is used to avoid running the ARM tests when building since Visual Studio doesn't install on ARM devices.  
##### utility.jam changes  
  
I added a small utility function to help wrapping a string in quotes.

---

## Comment 1

> Username: stgates  
> Created_at: 2014-05-27 22:01:59 UTC  
> Url: https://github.com/boostorg/build/pull/9#issuecomment-44341383  

Thanks for the feedback Vladimir. I'm going to break this down into a couple of smaller more easily reviewable changes incorporating your feedback.  
  
Steve

---
