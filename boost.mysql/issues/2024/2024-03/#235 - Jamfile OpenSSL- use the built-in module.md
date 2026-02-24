# #235 - Jamfile OpenSSL: use the built-in module [Closed]

> Username: anarthal  
> Created at: 2024-03-15 17:54:13 UTC  
> Updated at: 2024-03-27 15:43:38 UTC  
> Closed at: 2024-03-27 15:43:38 UTC  
> Url: https://github.com/boostorg/mysql/issues/235  

We currently use an ad-hoc strategy copied from an old version of Beast. This causes trouble if OpenSSL isn't present, as shown by the Boost Test Matrix.  
  
* Convert Jamfiles to use the built-in openssl module.  
* Add builds like the ones we have for CMake verifying that we don't fail if OpenSSL is not present.  
  
This will likely be required for the Boost modularization work that is undergoing.
