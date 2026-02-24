# #820 - boost 1.81.0 build error on windows [Closed]

> Username: egorpugin  
> Created at: 2022-12-14 20:19:01 UTC  
> Updated at: 2023-05-31 12:18:01 UTC  
> Closed at: 2023-05-31 12:18:01 UTC  
> Url: https://github.com/boostorg/json/issues/820  

vs2022 17.4.3.  
  
```  
When executing: [boost.json-1.81.0]/src/src.cpp  
D:\temp\29\.sw\src\2ccd1336e07b\json-31b10c1b70556dfee414ef12f83d85e7d6443508\include\boost/json/impl/value.ipp(403): error C2589: '(': illegal token on right side of '::'  
D:\temp\29\.sw\src\2ccd1336e07b\json-31b10c1b70556dfee414ef12f83d85e7d6443508\include\boost/json/impl/value.ipp(404): error C2062: type 'unknown-type' unexpected  
D:\temp\29\.sw\src\2ccd1336e07b\json-31b10c1b70556dfee414ef12f83d85e7d6443508\include\boost/json/impl/value.ipp(404): error C2059: syntax error: ')'  
```  
  
Probably `WIN32_LEAN_AND_MEAN` or `NOMINMAX` is missing.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-12-14 20:21:59 UTC  
> Url: https://github.com/boostorg/json/issues/820#issuecomment-1352109547  

Indeed, the problem is because of the use of std::min.

---

## Comment 2

> Username: egorpugin  
> Created at: 2022-12-14 20:24:35 UTC  
> Url: https://github.com/boostorg/json/issues/820#issuecomment-1352113370  

I've added NOMINMAX into my build as the less intrusive, it works.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-12-14 20:25:54 UTC  
> Url: https://github.com/boostorg/json/issues/820#issuecomment-1352114966  

There will probably be a patch very soon where this is fixed.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-12-20 07:35:32 UTC  
> Url: https://github.com/boostorg/json/issues/820#issuecomment-1358949549  

Fixed in #821. A patch for Boost release incoming.
