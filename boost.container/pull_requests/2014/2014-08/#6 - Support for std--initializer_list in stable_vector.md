# #6 Support for std::initializer_list in stable_vector [Closed]

> Username: robert-matusewicz  
> Created at: 2014-08-15 13:33:34 UTC  
> Updated at: 2014-08-15 16:19:51 UTC  
> Closed at: 2014-08-15 16:19:51 UTC  
> Url: https://github.com/boostorg/container/pull/6  

I miss support for std::initializer_list in some container methods, so I prepared patch that implements it for following method of stable vector (if the std::initializer_list is supported by the compiler):  
- constructor  
- operator=  
- assign  
- insert  
  I also added some simple tests, they pass with g++ and clang++.  
  
If this patch will pass a review and be included in boost::container repository I will add similar support for other boost containers.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-08-15 16:19:51 UTC  
> Url: https://github.com/boostorg/container/pull/6#issuecomment-52326566  

Thanks for the pull request. Added to develop branch in commit SHA-1: dd018538818aeab3c2e30c987a841e8d35c4eea4  
- Erased tab & removed unneeded clear() in assignment  
  
After merging, I removed a tag, updated some comments not updated to the latest changes and removed clear()+shrink_to_fit in assignment (I don't think they're needed).  
  
I've tested in several C++03 and C++11 without problems.

---
