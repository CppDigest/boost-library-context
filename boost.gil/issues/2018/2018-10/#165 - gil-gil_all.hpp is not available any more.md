# #165 - gil/gil_all.hpp is not available any more [Closed]

> Username: apolukhin  
> Created at: 2018-10-27 12:40:59 UTC  
> Updated at: 2018-10-27 18:49:27 UTC  
> Closed at: 2018-10-27 18:49:27 UTC  
> Url: https://github.com/boostorg/gil/issues/165  

Some of the tests in CI stopped compiling https://travis-ci.org/apolukhin/Boost-Cookbook/jobs/447029784#L3175 because of the missing `<boost/gil/gil_all.hpp>` header.  
  
Is that intended removal? What header should be used instead?  
  
### Minimal Working Example (in C++)  
  
https://github.com/apolukhin/Boost-Cookbook/tree/second_edition/Chapter12/07_gil  
  
### Actual behavior  
  
Does not compile  
  
### Expected  behavior  
  
Should compile and run  
  
### Environment  
  
All relevant information like:  
  
- Boost version (see `<boost/version.hpp>`): trunk  
- Compiler version: gcc-6  
- Build settings:

---

## Comment 1

> Username: mloskot  
> Created at: 2018-10-27 12:50:23 UTC  
> Updated at: 2018-10-27 12:52:01 UTC  
> Url: https://github.com/boostorg/gil/issues/165#issuecomment-433617644  

Thanks for the report. I will take a look in detail later tonight. Meanwhile, for Boost 1.68+, current master and develop branches, the all in one header is `boost/gil.hpp`
