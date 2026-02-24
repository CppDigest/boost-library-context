# #3 Support for std::initializer_list in stable_vector [Closed]

> Username: robert-matusewicz  
> Created at: 2014-08-11 20:46:41 UTC  
> Updated at: 2014-08-15 13:38:50 UTC  
> Closed at: 2014-08-15 13:38:50 UTC  
> Url: https://github.com/boostorg/container/pull/3  

I miss support for std::initializer_list in some container methods, so I prepared patch that implements it for following method of stable vector (if the std::initializer_list is supported by the compiler):  
- constructor  
- operator=  
- assign  
- insert  
  
I also added some simple tests, they pass with g++ and clang++.  
  
If this patch will pass a review and be included in boost::container repository I will add similar support for other boost containers.

---
