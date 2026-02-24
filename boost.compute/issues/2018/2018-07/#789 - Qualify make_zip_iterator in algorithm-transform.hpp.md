# #789 - Qualify make_zip_iterator in algorithm/transform.hpp [Closed]

> Username: Javanater  
> Created at: 2018-07-18 22:00:26 UTC  
> Updated at: 2018-07-20 00:17:06 UTC  
> Closed at: 2018-07-20 00:16:51 UTC  
> Url: https://github.com/boostorg/compute/issues/789  

Currently, transform calls make_zip_iterator. This is ambiguous if you include boost/compute/algorithm/transform.hpp and boost/iterator/zip_iterator.hpp in the same compilation unit. I was able to fix it by changing the make_zip_iterator calls to ::boost::compute::make_zip_iterator.

---

## Comment 1

> Username: kylelutz  
> Created at: 2018-07-19 05:07:57 UTC  
> Url: https://github.com/boostorg/compute/issues/789#issuecomment-406156409  

Thanks for the report!  
  
Fixed in #790.

---

## Comment 2

> Username: Javanater  
> Created at: 2018-07-20 00:17:06 UTC  
> Url: https://github.com/boostorg/compute/issues/789#issuecomment-406451715  

Thank you!
