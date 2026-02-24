# #458 - Some toolbox tests do not test anything [Closed]

> Username: mloskot  
> Created at: 2020-03-20 17:37:20 UTC  
> Updated at: 2020-03-21 21:53:10 UTC  
> Closed at: 2020-03-21 21:53:10 UTC  
> Url: https://github.com/boostorg/gil/issues/458  

### Actual behavior  
  
This should be self-explanatory  
  
https://github.com/boostorg/gil/blob/4ed7701b47764edf327e7c82f65cf9815026bdc2/test/extension/toolbox/gray_to_rgba.cpp#L18-L24  
  
https://github.com/boostorg/gil/blob/4ed7701b47764edf327e7c82f65cf9815026bdc2/test/extension/toolbox/gray_alpha.cpp#L17-L41  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
Presence of `BOOST_TEST` checks verifying computation results.  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Version (Git ref or `<boost/version.hpp>`): 1.72
