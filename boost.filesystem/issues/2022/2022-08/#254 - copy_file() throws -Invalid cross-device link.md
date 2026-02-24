# #254 - copy_file() throws "Invalid cross-device link" [Closed]

> Username: aroffringa  
> Created at: 2022-08-17 17:38:21 UTC  
> Updated at: 2022-10-19 17:07:02 UTC  
> Closed at: 2022-08-17 19:45:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254  

I get an "Invalid cross-device link" exception when calling `copy_file` if the source and destination are on different devices. I could imagine that moving a file could produce this error, but I don't understand it for `copy_file` -- as far as I understand this should be possible. I use Boost 1.74 from Debian testing (sorry it's not the newest but I checked and didn't see a previous ticket or fix for this particular issue, although there are some mentions of other fixes in `copy_file`).  
  
Here's an example exception:  
  
>  unhandled exception (type std::exception) in signal handler:  
> what: boost::filesystem::copy_file: Invalid cross-device link: "/home/anoko/Data/Flagging/local-rms.lua", "/home/anoko/.config/aoflagger/strategy.lua"  
>  
> Trace/breakpoint trap  
  
and the trace starting from where it enters boost:  
```  
#0  0x00007ffff60a9011 in __cxa_throw () at /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#1  0x00007ffff7f7ccbd in  () at /usr/lib/x86_64-linux-gnu/libboost_filesystem.so.1.74.0  
#2  0x00007ffff7f836b2 in boost::filesystem::detail::copy_file(boost::filesystem::path const&, boost::filesystem::path const&, unsigned int, boost::system::error_code*) ()  
    at /usr/lib/x86_64-linux-gnu/libboost_filesystem.so.1.74.0  
#3  0x00005555555fa03a in boost::filesystem::copy_file(boost::filesystem::path const&, boost::filesystem::path const&, boost::filesystem::copy_option)  
    (options=boost::filesystem::copy_option::overwrite_if_exists, to=..., from=...) at /usr/include/boost/filesystem/operations.hpp:353  
```  
  
It's called like this:  
```  
 boost::filesystem::copy_file(source, dest, boost::filesystem::copy_options::overwrite_existing);  
```

---

## Comment 1

> Username: aroffringa  
> Created at: 2022-08-17 17:45:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1218319562  

Btw I checked `std::filesystem_copy_file` on my system, and that function does allow cross-device copying.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-08-17 19:12:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1218393961  

Please test the latest Boost release or the code from develop. This should be fixed already.

---

## Comment 3

> Username: aroffringa  
> Created at: 2022-08-17 19:44:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1218421762  

Thanks for the reply -- I couldn't find it in the documentation. I rather not change my dev environment. In the end I've switched my project to std::filesystem, which fixes the issue for me. If you think it's already fixed, feel free to close the issue.

---

## Comment 4

> Username: aroffringa  
> Created at: 2022-08-17 19:52:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1218428844  

BTW for reference, I filed a Debian bug here: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1017558 .  
  
If you know in which version it is fixed, it might be useful for fixing Debian.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-08-17 19:56:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1218432641  

I think it was fixed in [1.77.0](https://www.boost.org/users/history/version_1_77_0.html).

---

## Comment 6

> Username: kkumar45  
> Created at: 2022-10-19 10:37:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1283793755  

> I think it was fixed in [1.77.0](https://www.boost.org/users/history/version_1_77_0.html).  
Is it possible to identify an independent patch for this issue? I am asking because it is not possible for me to update the Boost version in older releases of my application. So having a patch rather than upgrading the complete Boost would be useful.

---

## Comment 7

> Username: Lastique  
> Created at: 2022-10-19 17:07:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/254#issuecomment-1284321816  

It was probably fixed in 4b9052f1e0b2acf625e8247582f44acdcc78a4ce but there were many other fixes as well. I recommend you use a newer Boost release rather than trying to pick individual patches.
