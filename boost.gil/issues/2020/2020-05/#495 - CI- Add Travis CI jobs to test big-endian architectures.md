# #495 - CI: Add Travis CI jobs to test big-endian architectures [Closed]

> Username: mloskot  
> Created at: 2020-05-12 15:58:38 UTC  
> Updated at: 2023-09-30 09:43:51 UTC  
> Closed at: 2023-09-30 09:43:37 UTC  
> Url: https://github.com/boostorg/gil/issues/495  

It would be useful if we could run CI jobs testing GIL on big-endian, namely `s390x`:  
  
https://blog.travis-ci.com/2019-11-12-multi-cpu-architecture-ibm-power-ibm-z  
https://docs.travis-ci.com/user/multi-cpu-architectures/  
  
### References  
  
The big-aligned pixels (images) and byte-aligned packed pixels (images) seem broken on big-endian:  
  
- #431  
- #485  
  
Reports thanks to @trex58 who is trying Boost on big-endian AIX.

---

## Comment 1

> Username: mloskot  
> Created at: 2023-09-30 09:43:37 UTC  
> Url: https://github.com/boostorg/gil/issues/495#issuecomment-1741727311  

We have long departed from Travis, so this is not gonna happen.
