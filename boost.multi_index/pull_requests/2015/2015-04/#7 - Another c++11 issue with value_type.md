# #7 Another c++11 issue with value_type [Closed]

> Username: jhunold  
> Created at: 2015-04-23 08:40:32 UTC  
> Updated at: 2015-04-24 05:32:22 UTC  
> Closed at: 2015-04-24 05:32:22 UTC  
> Url: https://github.com/boostorg/multi_index/pull/7  

See #6 and 3f8d49acea7857383dbae904048d75f4c2099b51 for the first patch.  
Now operator= needs to use the typedef from the super class which in turn can not be private anymore

---

## Comment 1

> Username: joaquintides  
> Created_at: 2015-04-23 08:57:44 UTC  
> Url: https://github.com/boostorg/multi_index/pull/7#issuecomment-95496573  

Oh my, I definitely need to get me a C++11 compiler. My previous fix was bogus, I inserted the BOOST_DEDUCED_TYPENAME in the wrong member function. Could you please verify if https://github.com/boostorg/multi_index/commit/a26d8895847bd42015f7bd9a3651518679ddf7c2 fixes things up? Thank you for your help

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-04-24 05:32:21 UTC  
> Url: https://github.com/boostorg/multi_index/pull/7#issuecomment-95806042  

Fixed with https://github.com/boostorg/multi_index/commit/a26d8895847bd42015f7bd9a3651518679ddf7c2

---
