# #62 - master CI builds are broken; master dependencies require C++11 [Open]

> Username: jeking3  
> Created at: 2025-01-03 23:13:11 UTC  
> Updated at: 2025-01-03 23:13:11 UTC  
> Url: https://github.com/boostorg/pool/issues/62  

boostorg/pool is C++03, but CI in GHA and Appveyor identified one of the dependencies in master requires C++11  
CI also identified that in the develop branches this is not a problem and pool can stay at C++03  
Therefore we'll track this for the next release to make sure develop remains C++03 safe with CI builds
