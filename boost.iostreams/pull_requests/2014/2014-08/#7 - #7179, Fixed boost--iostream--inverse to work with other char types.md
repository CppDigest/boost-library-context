# #7 #7179, Fixed boost::iostream::inverse to work with other char types. [Merged]

> Username: coding-po  
> Created at: 2014-08-09 00:10:23 UTC  
> Updated at: 2016-12-09 07:50:20 UTC  
> Merged at: 2016-12-09 07:50:20 UTC  
> Closed at: 2016-12-09 07:50:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/7  



---

## Comment 1

> Username: eldiener  
> Created_at: 2016-12-03 23:54:28 UTC  
> Url: https://github.com/boostorg/iostreams/pull/7#issuecomment-264673963  

The read and write functions are not templated on a char_type.

---

## Comment 2

> Username: eldiener  
> Created_at: 2016-12-09 03:10:53 UTC  
> Url: https://github.com/boostorg/iostreams/pull/7#issuecomment-265922440  

I can see where the 'inverse' class does have a char_type. I will look at this further.

---
