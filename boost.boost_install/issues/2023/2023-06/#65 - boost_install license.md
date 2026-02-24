# #65 - boost_install license [Open]

> Username: rowaida1971  
> Created at: 2023-06-25 05:50:58 UTC  
> Updated at: 2023-06-25 07:08:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/65  

Hi,  
We build boost offline and with newer boost versions, the submodules are not all available, so we need to have the packages for the submodules as well, but we have strict license rules and can't use unlicensed packages.  
Could you add LICENSE.txt file similar to https://github.com/boostorg/build?  
  
Thank you.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-06-25 07:08:17 UTC  
> Url: https://github.com/boostorg/boost_install/issues/65#issuecomment-1605904286  

As a general rule, we don't add LICENSE files to boostorg submodules. Each file has a copyright preamble though. E.g.  
  
https://github.com/boostorg/boost_install/blob/05799a9b0148bd67be52d3f0fe1576194523b63e/BoostConfig.cmake#L1-L3  
https://github.com/boostorg/boost_install/blob/05799a9b0148bd67be52d3f0fe1576194523b63e/BoostDetectToolset.cmake#L1-L3  
https://github.com/boostorg/boost_install/blob/05799a9b0148bd67be52d3f0fe1576194523b63e/Jamfile#L1-L3  
https://github.com/boostorg/boost_install/blob/05799a9b0148bd67be52d3f0fe1576194523b63e/boost-install-dirs.jam#L1-L3  
https://github.com/boostorg/boost_install/blob/05799a9b0148bd67be52d3f0fe1576194523b63e/boost-install.jam#L1-L3
