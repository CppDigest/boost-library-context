# #197 - Accessing items in bins [Closed]

> Username: nuhash  
> Created at: 2019-06-03 16:23:36 UTC  
> Updated at: 2019-06-11 12:07:53 UTC  
> Closed at: 2019-06-11 12:07:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/197  

I'm creating histograms of colours within a patch of an image. I am using a 3D histogram (each axis is a colour channel) and I want to find the colours inside the most populated bin.  
  
I haven't been able to find any way to do this. But since you can rebin, I assume there must be away to do it.  
  
The alternative is that I take the bounds of the most populated bin and see which of the pixels fall within in it.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-06-03 19:09:45 UTC  
> Updated at: 2019-06-04 12:04:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-498386963  

Hi, you can use the `indexed` range generator. See https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.fill_a_histogram_and_access_cell  
  
```c++  
#include <boost/histogram.hpp>  
  
using namespace boost::histogram;  
using namespace boost::histogram::literals;  
  
int main() {  
    auto h = make_histogram(  
        axis::integer<>(0, 255, "r"),  
        axis::integer<>(0, 255, "g"),  
        axis::integer<>(0, 255, "b")  
    );  
  
    // fill ...  
  
    // find colors for max element  
    double max = 0;  
    struct rgb_t { int r, g, b; };  
    rgb_t color = {-1, -1, -1};  
    for (auto&& x : indexed(h)) {  
        if (*x < max) continue;  
        max = *x;  
        color = { x.bin(0_c), x.bin(1_c), x.bin(2_c) };  
    }  
    // color contains now the rgb values for the bin with the highest count  
}  
```  
It would be nice to use `std::max_element` here, but that doesn't work with the current version of the library, since `std::max_element` requires forward iterators, while `indexed` returns input iterators. There is an open issue to fix that, see #198

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-06-03 19:13:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-498388357  

This seems like a useful item for the "Getting Started" section to showcase `indexed`. Thank you for reporting!

---

## Comment 3

> Username: nuhash  
> Created at: 2019-06-04 10:48:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-498620964  

Thank you for that. I was using bins which spanned across more than one colour but this would actually work better.  
  
However, if someone was using bins which span multiple values then how would they find the entries? I imagine this might be something of interest to quite a few usages of histograms.

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-06-04 10:48:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-498621194  

> However, if someone was using bins which span multiple values then how would they find the entries? I imagine this might be something of interest to quite a few usages of histograms.  
  
Could you please send a minimal code example? I don't know what you mean.

---

## Comment 5

> Username: nuhash  
> Created at: 2019-06-04 11:18:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-498629584  

```  
auto h = make_histogram(axis::regular<>(nBins,0.0,1.0,"R"),axis::regular<>(nBins,0.0,1.0,"G"),axis::regular<>(nBins,0.0,1.0,"B"));`  
//fill  
int modeCount = -1;  
int modeIndex0;  
int modeIndex1;  
int modeIndex2;  
  
for(auto item : indexed(h)){  
    int c = item.bin();  
    if(*item>modeCount)  
    {  
         modeCount = *item;  
         modeIndex0 = item.bin(0_c);  
         modeIndex1 = item.bin(1_c);  
         modeIndex2 = item.bin(2_c);  
     }  
}  
  
auto itemsInModeBin = h.ItemsInBin(modeIndex0,modeIndex1,modeIndex2);  
  
ColorStruct meanColorInModeBin  
for(auto item : itemsInModeBin)  
{  
//compute average of colors in bin  
}  
```  
  
Hope this helps you understand my question

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-06-04 12:01:11 UTC  
> Updated at: 2019-06-04 12:01:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-498641899  

I think there is a misunderstanding then. To access the cell for `modeIndex0, modeIndex1, modeIndex2` , you use `histogram::at`. In a standard histogram, the cell just contains a counter. Whenever you fill the histogram, it looks up the cell and increments the counter. The counter is just a number, so there is nothing to iterate over in the last loop of your example.  
  
You can - however - make a generalized histogram, where you pass something to the cell during filling and where you can then do something with those cell contents. Please read https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_accumulators  
The last example shows how to make a custom accumulator.

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-06-07 13:31:33 UTC  
> Updated at: 2019-06-07 13:47:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-499885699  

With the current `develop` branch, you can now do  
```  
#include <boost/histogram.hpp>  
#include <algorithm>  
#include <iostream>  
  
using namespace boost::histogram;  
using namespace boost::histogram::literals;  
  
int main() {  
    auto h = make_histogram(  
        axis::regular<>(100, 0, 1, "r"),  
        axis::regular<>(100, 0, 1, "g"),  
        axis::regular<>(100, 0, 1, "b")  
    );  
  
    // fill ...  
  
    // find colors for max element  
    auto ind = indexed(h);  
    auto max_it = std::max_element(ind.begin(), ind.end());  
    // access colors  
    std::cout << "count=" << **max_it   
              << ", r=" << max_it->bin(0)  
              << ", g=" << max_it->bin(1)  
              << ", b=" << max_it->bin(2) << std::endl;  
}  
```

---

## Comment 8

> Username: HDembinski  
> Created at: 2019-06-11 12:07:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/197#issuecomment-500812430  

Since there is no response, I am closing this.
