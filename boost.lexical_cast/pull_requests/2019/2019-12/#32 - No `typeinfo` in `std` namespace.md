# #32 No `typeinfo` in `std` namespace [Merged]

> Username: Kojoley  
> Created at: 2019-12-02 18:32:50 UTC  
> Updated at: 2019-12-03 11:15:21 UTC  
> Merged at: 2019-12-03 04:57:54 UTC  
> Closed at: 2019-12-03 04:57:55 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/32  

Fixes https://svn.boost.org/trac10/ticket/4115 without relying on Boost.Config  
to inject `::typeinfo` into `std` namespace.  
  
The problem could be reproduced by applying https://github.com/boostorg/lexical_cast/pull/31 and https://github.com/boostorg/config/pull/307 on VC9.0/10.0/11.0.

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-12-02 19:39:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/lexical_cast/pull/32#pullrequestreview-325521619  

📁 include/boost/lexical_cast/bad_lexical_cast.hpp

```diff
  62 | #ifndef BOOST_NO_TYPEID
  63 |+ #ifdef BOOST_NO_STD_TYPEINFO
  64 |+ # define BOOST_LEXICAL_CAST_TYPE_INFO ::type_info
```

> Username: apolukhin  
> Created_at: 2019-12-02 19:39:17 UTC  
> Updated_at: 2019-12-02 19:44:25 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/32#discussion_r352789557  

How about a  
```  
namespace detail {  
struct type_info_workaround {   
#ifdef BOOST_NO_STD_TYPEINFO  
    typedef ::type_info type;  
#else  
    typedef ::std::type_info type;   
#endif  
};  
}  
```  
There's less macro in that way

> Username: Kojoley  
> Created_at: 2019-12-02 19:45:15 UTC  
> Updated_at: 2019-12-02 19:45:16 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/32#discussion_r352792305  

Agree, I have updated the code, please take a look.


---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-12-03 04:58:19 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/32#issuecomment-560998893  

Thank you for the work!

---
