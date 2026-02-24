# #11 Disable auto_ptr support in get_pointer when BOOST_NO_AUTO_PTR is def… [Merged]

> Username: awulkiew  
> Created at: 2015-06-05 12:10:46 UTC  
> Updated at: 2015-06-05 12:31:34 UTC  
> Merged at: 2015-06-05 12:31:27 UTC  
> Closed at: 2015-06-05 12:31:27 UTC  
> Url: https://github.com/boostorg/core/pull/11  

…ined.  
  
This is consistent with how Boost.SmartPtr handles deprecated std::auto_ptr. This way the user can disable the auto_ptr support to suppress compiler warnings.

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-06-05 12:31:34 UTC  
> Url: https://github.com/boostorg/core/pull/11#issuecomment-109280658  

Thanks Adam.

---
