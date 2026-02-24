# #136 - Compile-time option to disable SSL completely [Open]

> Username: ysc3839  
> Created at: 2023-08-07 08:20:03 UTC  
> Updated at: 2024-01-10 19:55:49 UTC  
> Url: https://github.com/boostorg/redis/issues/136  

Currently even if SSL is not used, the library still requires OpenSSL. Can you provide a compile-time option to disable SSL completely?

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-09-04 12:09:37 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1705158935  

I am not finding the time to implement this right now as I have other priorities in the project. It might take some time.

---

## Comment 2

> Username: RayDavidWhitmer  
> Created at: 2024-01-02 20:46:56 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1874529122  

I would vote for this fix. While openssl is easy to provide in a Linux environment, this is less the case in Windows. When building libcurl, for example, there are options besides openssl which integrate better with the native Windows environment.  
  
It would be nice for asio to support options besides openssl which are more native to Windows, like libcurl does.  
  
It would be also nice to also be able to avoid unnecessary dependencies between boost::redis and anything for those who didn't need an encrypted connection to redis in the first place.  
  
When I include redis.hpp, it winds up including detail/handshaker.hpp which includes asio/ssl/context.hpp which includes asio/ssl/context_base.hpp which includes asio/ssl/detail/openssl_types.hpp which tries to include openssl_conf.h which is not likely to be on my Windows systems.  
  
Eliminating the hard dependency either in favor of nothing if ssl is not required (easier fix?) or in favor of Windows facilities, like CURL does, would make the redis libraries usable more broadly.  
  
I can investigate whether I have the permission and time to propose specific modifications for one or both of these.

---

## Comment 3

> Username: RayWhitmer  
> Created at: 2024-01-03 20:28:33 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1875930664  

The asio/openssl dependencies as written seem too convoluted for me to reasonably to fix. I think someone else may need to address making ssl code truly optional (and/or to optionally use schannel on windows) starting in the asio ssl layer which is getting used all over the place on paths where the functionality is NOT conditional on ssl functionality.

---

## Comment 4

> Username: mzimbres  
> Created at: 2024-01-03 21:04:29 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1875970188  

Hi, if https://wintls.dev/ gets included in Boost it would be simpler to get rid of openssl. Otherwise, the problem is that I have to use `asio::stream<tcp::stream>` to support dynamically switching from one to the other. However, I don't think this is too difficult ifdef openssl away in Boost.Redis, it is just a matter of my own free time. I am going to have a look at how Boost.MySql (@anarthal ) solved this.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-01-03 21:13:27 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1875980452  

I never solved it. MySQL needs hashing functions regardless of whether you're using TLS or not, and reimplementing sha256 wasn't something I wanted. I may change my mind at some point (since people also asked for it).  
  
I don't know if this helps, but `ssl::stream<tcp::socket&>` is a valid idiom I've found useful for conditionally enabling SSL, at least at runtime.

---

## Comment 6

> Username: mzimbres  
> Created at: 2024-01-03 21:25:00 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1875991991  

@ysc3839 Do you need this to get rid of openssl on windows or in general? AFAICS  https://wintls.dev/ would solve most peoples problems.  
  
@laudrup Do you have any idea when are you going to submit Boost.Wintls for inclusion in Boost?

---

## Comment 7

> Username: ysc3839  
> Created at: 2024-01-04 07:56:41 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1876664467  

@mzimbres It's not for Windows. My program does not use TLS. By disabling SSL, the program would not have external dependency (the only dependency is headers-only part of boost), so it can run on many Linux distro without rebuild. And maybe it can run on Windows.

---

## Comment 8

> Username: laudrup  
> Created at: 2024-01-06 12:55:24 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1879674280  

@mzimbres   
  
I did consider trying to get Boost.Wintls included in Boost at some point but it's not really something I've done anything about. I think it might make more sense if it could be included in Asio (standalone and Boost) but I haven't done anything about that either.  
  
Seeing as you are the maintainer of a Boost library I assume you have some experience with getting a library included with Boost. Would you recommend that I try to get my Boost.Wintls library included and if so do you think you could help me submitting it?  
  
Thanks a lot.

---

## Comment 9

> Username: anarthal  
> Created at: 2024-01-06 13:01:03 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1879675675  

I'd recommend that you asked in Boost's mailing list first if you haven't before. You can act in consequence depending on the answers you get. If you move forward with getting it into Boost, I may be able to provide some guidance to you, too.

---

## Comment 10

> Username: laudrup  
> Created at: 2024-01-06 13:05:03 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1879676817  

@anarthal   
  
Thanks. First of all I was mostly interested in knowing whether it might make sense to even try getting it included but I guess the mailing list might be a good way to get some feedback on that as well. I'll probably try writing there at some point.

---

## Comment 11

> Username: anarthal  
> Created at: 2024-01-06 14:01:00 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1879696141  

I think it can be useful, either as a standalone library or as part of asio.

---

## Comment 12

> Username: mzimbres  
> Created at: 2024-01-06 15:17:37 UTC  
> Updated at: 2024-01-06 15:31:54 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1879726132  

@chriskohlhoff Do you think  https://wintls.dev could be incorporated by Asio?

---

## Comment 13

> Username: mzimbres  
> Created at: 2024-01-06 15:31:31 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1879729858  

> Seeing as you are the maintainer of a Boost library I assume you  
> have some experience with getting a library included with Boost.  
> Would you recommend that I try to get my Boost.Wintls library  
> included and if so do you think you could help me submitting it?  
  
I think Boost.Wintls fits well into Boost. I am not windows user, but as you have seen above there are people that don't want to depend on openssl on windows. If it is part of Boost I could use it as the default stream type on Windows.  
  
As others said, announce your library on the [Boost developer's ML](https://lists.boost.org/mailman/listinfo.cgi/boost) and ask for endorsement. After that you can ask for a review manager, which will arrange a review with the wizards.  Reviews last for 10 days usually and you have to answer a lot of questions.  
  
After the library is accepted you have to integrate it in Boost, but there is no time pressure and the whole process might take one year.

---

## Comment 14

> Username: laudrup  
> Created at: 2024-01-10 19:55:48 UTC  
> Url: https://github.com/boostorg/redis/issues/136#issuecomment-1885620738  

> I think Boost.Wintls fits well into Boost. I am not windows user, but as you have seen above there are people that don't want to depend on openssl on windows. If it is part of Boost I could use it as the default stream type on Windows.  
  
I'm not a Windows user either and don't have much experience developing anything for Windows. The reason I wrote this in the first place was that I write cross platform C++ at work where native TLS support on Windows might be relevant. I don't have any use for this library myself personally or at work currently, but of course I'm very happy if any work I've done can actually be used by someone.  
  
> As others said, announce your library on the [Boost developer's ML](https://lists.boost.org/mailman/listinfo.cgi/boost) and ask for endorsement. After that you can ask for a review manager, which will arrange a review with the wizards. Reviews last for 10 days usually and you have to answer a lot of questions.  
  
I'll consider doing that sooner or later. I think it might make more sense to integrate it in Asio instead but I guess that discussion could be had on the mailing list as well.  
  
Thanks a lot for your encouragement, I really appreciate it.
