# #202 - New hashing algorithm [Open]

> Username: sdkrystian  
> Created at: 2020-08-24 19:04:46 UTC  
> Updated at: 2021-03-04 05:36:05 UTC  
> Url: https://github.com/boostorg/json/issues/202  

We currently use FNV-1A, but there are much faster algorithms out there that we can use (e.g. murmurhash or seahash).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-24 19:07:10 UTC  
> Url: https://github.com/boostorg/json/issues/202#issuecomment-679310632  

murmur has a vulnerability

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-27 15:46:35 UTC  
> Url: https://github.com/boostorg/json/issues/202#issuecomment-682031852  

The Dimov opinion: Try xxhash32/64

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-09-05 23:05:04 UTC  
> Url: https://github.com/boostorg/json/issues/202#issuecomment-687674029  

Peter has settled on murmur2
