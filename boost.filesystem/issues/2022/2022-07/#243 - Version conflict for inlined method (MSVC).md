# #243 - Version conflict for inlined method (MSVC) [Closed]

> Username: Nekto89  
> Created at: 2022-07-19 14:44:32 UTC  
> Updated at: 2022-07-19 20:09:39 UTC  
> Closed at: 2022-07-19 16:47:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/243  

I have linking problem with boost::filesystem::path::extension.  
Under some circumstances final binary starts using extension_v4 instead of extension_v3. I've enforced BOOST_FILESYSTEM_VERSION=3 for every cpp in my project. It seems that linking order affects this behavior in some way.  
  
My assumption is that in some cases MSVC doesn't inline "extension()" during optimization step and takes version from libboost_filesystem.lib which is v4.   
  
![image](https://user-images.githubusercontent.com/4276548/179778521-88ac104e-b098-491c-a41a-a12117474d37.png)

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-19 16:47:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/243#issuecomment-1189324409  

`path::extension`, whether it is inlined or not, forwards to a versioned `path::extension_vN` method. If some of your code uses v3 and some uses v4 then you have a configuration issue. Sorry, I cannot help you with your project. Check if there are libraries built with different Boost.Filesystem versions. Also check if there's a problem with precompiled headers.  
  
Also note that *internally* Boost.Filesystem is v4. That is if some Boost.Filesystem algorithm calls `path::extension` internally, it will call `path::extension_v4`. This is expected and consistent regardless of what `BOOST_FILESYSTEM_VERSION` is defined to by the user of the library.

---

## Comment 2

> Username: Nekto89  
> Created at: 2022-07-19 20:09:39 UTC  
> Url: https://github.com/boostorg/filesystem/issues/243#issuecomment-1189504812  

having method that has different implementation with same signature is what causing this problem in the first place in static configuration.  
  
I'll rollback to Boost 1.74.0 where there was no such problem and will try to get rid of Boost.Filesystem in favor of std then.
