# #279 - cannot add histograms with growing category axis that store categories in different order [Closed]

> Username: mreininghaus  
> Created at: 2020-05-01 09:42:38 UTC  
> Updated at: 2020-05-20 00:48:02 UTC  
> Closed at: 2020-05-20 00:48:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/279  

Hi Hans,  
if I have two histograms with a growing axis of type "category" and over time their index sets diverge, they cannot be added anymore. Here is a simple example:  
```  
#include <boost/histogram.hpp>  
  
int main() {  
  auto a = boost::histogram::make_histogram(  
      boost::histogram::axis::category<  
          int, boost::histogram::use_default,  
          boost::histogram::axis::option::growth_t>{});  
  
  auto b = a;  
  
  a(4);  
  b(4);  
  
  auto c = a + b; // works  
  
  b(6);  
  auto d = a + b; // doesn't work: "axes of histograms differ"  
  
  return 0;  
}  
```  
In my opinion it makes sense (and in fact I expected this to happen) to merge the two sets.

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-05-03 15:21:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/279#issuecomment-623126178  

I agree that it is a "bug" in the sense of unexpected behavior. This will be fixed together with #251. Just to explain why it does not work and how to work around for now. If you use growing category axes, the categories are added in order of first encounter in the data stream. If you fill histograms in independent threads on independent data, then the order of encounter will in general be different, and then the axes are not compatible anymore. Adding histograms is currently implemented in a very simple and fast way, because we only allow adding histograms with the same cell layout. Allowing to add histograms with different layout makes only really sense for category axes, and it is a bit annoying to write a special case for this one axis type. Eventually it will happen, but it is not super easy.  
  
As a workaround, don't use growing category axes in multi-threaded/multi-process environments. Instead, find out what categories are there in the data and then pass that list to the category axis. Filling histograms is going to be noticably faster and you won't have issues with adding histograms.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-05-20 00:48:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/279#issuecomment-631167968  

fixed in develop
