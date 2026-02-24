# #413 - move `basic_parser` out of namespace `detail` [Closed]

> Username: akrzemi1  
> Created at: 2020-09-24 10:47:19 UTC  
> Updated at: 2020-09-24 18:19:28 UTC  
> Closed at: 2020-09-24 18:19:28 UTC  
> Url: https://github.com/boostorg/json/issues/413  

The convention in Boost has always been that namespace `detail` contains implementation details that can change without notice at any time.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-24 14:57:11 UTC  
> Url: https://github.com/boostorg/json/issues/413#issuecomment-698399050  

`basic_parser` is in `json` not `detail`
