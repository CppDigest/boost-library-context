# #74 Add missing sp_typeinfo_.hpp includes [Closed]

> Username: MarcelRaad  
> Created at: 2019-12-05 08:43:54 UTC  
> Updated at: 2019-12-05 15:08:14 UTC  
> Closed at: 2019-12-05 15:05:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74  

Some of these are included indirectly, but others are not.  
Broken in commit e306b30dcf0.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2019-12-05 12:13:03 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562103851  

The clang-win AppVeyor failures look unrelated to me.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-05 13:36:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562131077  

How was this "broken"? The patch didn't change or remove any includes, and there were no failures.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2019-12-05 14:25:04 UTC  
> Updated_at: 2019-12-05 14:26:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562150920  

What I get for `#include <boost/shared_array.hpp>` is:  
  
```  
In file included from [...]/boost/shared_array.hpp:17:  
[...]/boost/smart_ptr/shared_array.hpp:228:50: error: no type named 'sp_typeinfo_' in namespace 'boost::detail'; did you mean 'sp_typeinfo'?  
    void * _internal_get_deleter( boost::detail::sp_typeinfo_ const & ti ) const BOOST_SP_NOEXCEPT  
                                  ~~~~~~~~~~~~~~~^  
```  
  
Probably boost/detail/sp_typeinfo.hpp got included indirectly there?

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-12-05 14:38:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562156907  

Thanks, I'll take a look to see what changed there.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-12-05 14:45:29 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562159695  

Can you please trace the includes? I don't get the error; the include chain is supposed to go shared_array.hpp -> detail/shared_count.hpp -> detail/sp_counted_base.hpp -> detail/sp_counted_base_{impl}.hpp -> detail/sp_typeinfo_.hpp.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-12-05 14:50:30 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562161898  

As far as I can see, all sp_counted_base_{impl}.hpp files include detail/sp_typeinfo_.hpp, so there must be something else going on here.

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2019-12-05 14:59:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562166082  

I only noticed this on a build machine I don't have direct access to. Good idea to trace the includes - it looks like this is not Boost's fault at all. A precompiled header didn't get recompiled automatically after the Boost update from 1.70 to 1.71, which included the 1.70 version of boost/thread/mutex.hpp, which ended up including the 1.70 version of sp_counted_base_clang.hpp through shared_ptr.hpp, which was fixed in the commit mentioned above. Sorry for the false alarm!

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-12-05 15:05:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/74#issuecomment-562168714  

Glad we sorted this out. :-)

---
