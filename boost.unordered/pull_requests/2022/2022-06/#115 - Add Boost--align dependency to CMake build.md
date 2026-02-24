# #115 Add Boost::align dependency to CMake build [Closed]

> Username: Flamefire  
> Created at: 2022-06-02 15:03:11 UTC  
> Updated at: 2022-06-02 15:28:43 UTC  
> Closed at: 2022-06-02 15:28:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/115  

This is required after #112  
  
@cmazakas @pdimov Please merge soon as this currently breaks the super-project CMake build

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-06-02 15:18:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/115#issuecomment-1144988254  

Not sure that the use of Align is intended.  
```  
align:  
    <boost/align/alignment_of.hpp>  
        from <boost/unordered/detail/fca.hpp>  
```  
alignment_of is in TypeTraits.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-06-02 15:23:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/115#issuecomment-1144993544  

Yeah, the include is unnecessary as the code doesn't use it. It uses `boost::alignment_of` from TypeTraits. https://github.com/boostorg/unordered/blob/e36e3bcf96ef1d0655544d6ae6ef0a2fa14ccda5/include/boost/unordered/detail/fca.hpp#L409

---

## Comment 3

> Username: Flamefire  
> Created_at: 2022-06-02 15:28:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/115#issuecomment-1144998362  

Oh, right. --> #116

---
