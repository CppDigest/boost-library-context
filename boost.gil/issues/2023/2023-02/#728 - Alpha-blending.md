# #728 - Alpha-blending [Open]

> Username: SergMariaDB  
> Created at: 2023-02-16 12:26:17 UTC  
> Updated at: 2023-02-20 12:37:16 UTC  
> Url: https://github.com/boostorg/gil/issues/728  

### Is your feature request related to a problem? Please describe.  
Alpha-blending seem chellengable for this enormous support of the different types of channels.  
<!-- A clear and concise description of what the problem is. Ex. I'm always frustrated when [...] -->  
  
### Describe the solution you'd like  
How about start on support the same RGB views?  
<!-- A clear and concise description of what you want to happen. -->  
  
#### C++ Example  
template <class ViewT>  
auto alpha_blend(const ViewT& view1, const ViewT& view2, ptr_diff_t x=0, ptr_diff_t y=0){  
  ...  
}  
<!-- C++ program or snippet that shows preferred solution -->  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main  
{  
    ...  
    auto v3 = alpha_blend(v1, v2);  
    ...  
}  
```  
  
### Describe alternatives you've considered  
I imagine using boost::compute for this.  
<!-- A clear and concise description of any alternative solutions or features you've considered. -->  
  
### Additional context  
https://stackoverflow.com/questions/746899/how-to-calculate-an-rgb-colour-by-specifying-an-alpha-blending-amount  
<!-- Add any other context or screenshots about the feature request here. -->

---

## Comment 1

> Username: mloskot  
> Created at: 2023-02-16 12:50:51 UTC  
> Url: https://github.com/boostorg/gil/issues/728#issuecomment-1433041588  

> How about start on support the same RGB views?  
  
There is nothing wrong with features specific or limited to narrow set of specific pixel/view types.  
Starting small with a proof of concept, well tested, documented...it's actually a common and appreciated practice, and not only in GIL, I guess.

---

## Comment 2

> Username: ohhmm  
> Created at: 2023-02-16 13:42:02 UTC  
> Url: https://github.com/boostorg/gil/issues/728#issuecomment-1433106145  

Consider it for GSOC. Thanks.

---

## Comment 3

> Username: SergMariaDB  
> Created at: 2023-02-20 08:27:45 UTC  
> Url: https://github.com/boostorg/gil/issues/728#issuecomment-1436539280  

Found one alpha-blending implementation for boost::gil here https://github.com/boost-gil/gil-contributions-archive/blob/sandbox/boost/gil/extension/toolbox/blend.hpp  
  
Here's boost::compute based image generation sample: https://github.com/ohhmm/generator/blob/5ecaa727352d34fd0b0f134c9b8ceed4a202f475/picture-pattern-generate/generator.cpp#L55  
  
Any plans to use boost::compute for boost::gil?

---

## Comment 4

> Username: mloskot  
> Created at: 2023-02-20 12:37:15 UTC  
> Url: https://github.com/boostorg/gil/issues/728#issuecomment-1436945174  

Yes, the github.com/boost-gill is our sister org. I forgot there are some blending implementations indeed.  
  
> Any plans to use boost::compute for boost::gil?  
  
No plans, AFAIK, but it's possible:  
  
GIL can accept variety of features via extensions, including domain-specific features, features with new dependencies, features with non-Boost externals as dependencies. So, I'd suggest to consider the Boost.Compute-based blending as an extension first.   
It is easy to move features from extension to core, but once a feature is in core, then removing it is not easy. See more on core vs extensions https://github.com/boostorg/gil/discussions/668
