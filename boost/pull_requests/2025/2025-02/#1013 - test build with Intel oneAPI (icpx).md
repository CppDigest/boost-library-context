# #1013 test build with Intel oneAPI (icpx) [Closed]

> Username: MarDiehl  
> Created at: 2025-02-22 05:23:08 UTC  
> Updated at: 2025-07-30 14:37:03 UTC  
> Closed at: 2025-07-30 14:37:03 UTC  
> Url: https://github.com/boostorg/boost/pull/1013  

The Intel compiler suite is often used on clusters. Since the re-branding to "oneAPI", it is available at no cost and Intel offers containers on DockerHub. This allows easy integration into GitHub actions.  
  
This PR ensures that the Boost libraries can at least be build with CMake. Further enhancements could include  
- building with b2  
- running the test suite  
  
Currently, the test suite fails. Hence, probably fixes (and, ideally enhancements to the CI) for some of the subpackages are needed first.  
  
  
see #952 and #615

---
