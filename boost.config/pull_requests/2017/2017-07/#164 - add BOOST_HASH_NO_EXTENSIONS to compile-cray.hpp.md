# #164 add BOOST_HASH_NO_EXTENSIONS to compile/cray.hpp [Merged]

> Username: rsdale  
> Created at: 2017-07-20 19:48:34 UTC  
> Updated at: 2019-11-02 11:16:09 UTC  
> Merged at: 2017-07-20 19:48:55 UTC  
> Closed at: 2017-07-20 19:48:56 UTC  
> Url: https://github.com/boostorg/config/pull/164  

Avoid problems with multiple definitions of std::list using gnu headers.  
	modified:   cray.hpp

---

## Comment 1

> Username: danieljames  
> Created_at: 2017-07-20 20:01:58 UTC  
> Url: https://github.com/boostorg/config/pull/164#issuecomment-316814077  

This is probably the wrong solution, but you shouldn't set hash macros in config anyway.

---

## Comment 2

> Username: rsdale  
> Created_at: 2017-07-20 20:17:10 UTC  
> Url: https://github.com/boostorg/config/pull/164#issuecomment-316817780  

I will look for a better solution.  
  
I've got a problem with multiple definitions of std::list that seems   
related to the  _GLIBCXX_USE_CXX11_ABI macro.  
I get that this is a hack, but it will allow my customers to compile   
their applications...and it's the best I can do today.  
  
Thanks for your note.  
--  
regards,  
rsd  
  
  
  
On 07/20/2017 03:01 PM, Daniel James wrote:  
>  
> This is probably the wrong solution, but you shouldn't set hash macros   
> in config anyway.  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/config/pull/164#issuecomment-316814077>,   
> or mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AFwGBKdVW9SvO1kpV70KpjGxowco09M6ks5sP7I3gaJpZM4Oelf0>.  
>

---
