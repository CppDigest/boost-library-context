# #277 - Allow to iterate over the indexed range from an arbitrary location [Closed]

> Username: chiarazampolli  
> Created at: 2020-04-20 12:12:35 UTC  
> Updated at: 2021-04-24 20:37:43 UTC  
> Closed at: 2021-04-24 20:37:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/277  

(Following the conversation in https://gitter.im/boostorg/histogram).  
This issue concerns 2D histograms, in particular the use case of calculating integrals (--> sum of the bin entries) for specific ranges over x and y.   
For now, there are 2 ways:   
  
1. reduce the histogram and then use the std::accumulate --> not to be used, too slow, due to the reduction step  
  
2. use the indexed iterator, skipping the bins if not in the interesting range --> faster approach, but not 100% efficient yet, as one cannot e.g. jump directly to the item from which to start the sum, or skip uninteresting bins (e.g. `if "x = indexed(histo)", x += something` does not work).  
  
In other words, suppose that we have:  
  
`auto histo = boost::histogram::make_histogram(boost::histogram::axis::regular<>(20, -10, 10, "axis0"), boost::histogram::axis::integer<>(0, 30, "axis1"));`  
  
and we need the sum on axis0 in [0, 10] and on axis1 in [20, 22], it would be good to have a way to do it efficiently.  
  
Thanks!  
  
Chiara

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-04-22 16:22:19 UTC  
> Updated at: 2020-04-22 16:23:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-617882951  

Thank for reporting this. I think you revealed a missing feature in the interface here. In Boost we always strife for zero-overhead, whatever you want to do, the library should do it at least as efficiently as some hand-written code. Doing an integration over a rectangular region in a histogram should work by finding the iterators to the begin and end of that region and use either `std::accumulate` or a for-loop over an `indexed_range`.

---

## Comment 2

> Username: chiarazampolli  
> Created at: 2020-04-24 20:39:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-619228502  

Hello Hans,   
  
Thanks for your feedback. For the sake of clarity, it would be good that the "user" has the possibility to iterate on a restricted range of the indexed_range, jumping by a known amount also. The general use case is to iterate over a specific sub-range of the indexed_range. I am pointing this out to be sure that the implementation following my request will allow to iterate in a defined region, not just for example providing an "integral" method (which is not the my only use case).  
  
Cheers,  
  
Chiara

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-04-27 16:02:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-620078744  

Thanks for clarifying, I understand you.  
  
After thinking more about this, I remembered why this request is not super trivial, one needs a special kind of iterator to iterate only over the bins in an inner rectangular region. Good news is we have that functionality already in the iterators provided by `indexed_range`, since we need the same functionality to skip the *flow bins.

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-04-27 16:07:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-620081621  

The workaround for now is to use something like this for a 2D histogram  
```c++  
for (unsigned j = axis_1_begin; j != axis_1_end; ++j) {  
  for (unsigned i = axis_0_begin; i != axis_0_end; ++i) {  
    const auto& v = h.at(i, j);  
    // do something with v  
  }  
}   
```

---

## Comment 5

> Username: chiarazampolli  
> Created at: 2020-04-27 16:11:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-620083879  

Dear @HDembinski  ,   
  
Thanks! Indeed, I did not try this, which looks quite simple. I will try it, and let you know.   
  
Chiara

---

## Comment 6

> Username: chiarazampolli  
> Created at: 2020-04-27 21:21:32 UTC  
> Updated at: 2020-05-04 14:31:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-620241964  

I tried the workaround, and indeed it is extremely fast. The implementation I had till now was:  
  
```c++  
  int counts = -1;  
  for (auto&& x : indexed(mHisto[sector])) {  // mHisto[sector] is the 2D boost histo  
    counts++;  
    if (counts < startCount) continue; // startcount is the index of the first element of interest in the iterator  
    if (x.bin(0).lower() > xmax && ymin == ymax) { // all others also will be > but only if ymin = ymax; in the other cases, we should jump to the next row,which for now we cannot do in boost  
      break;  
    }  
    if (x.index(1) > ymax) { // we passed the needed ybin  
      break;  
    }  
    if ( (x.bin(0).upper() > xmin) && (x.bin(0).lower() <= xmax) && (x.index(1) >= ymin)) { // I have to keep the condition "&& (x.bin(0).lower() <= xmax)" because I can break only if ymin == ymax  
      res1 += x.get(); \\ res1 is my integral  
    }  
  }  
```  
I replaced it with the loop you suggested.   
Do you plan to provide an example with the indexed_range iterator too?  
  
Thanks a lot!  
  
Chiara

---

## Comment 7

> Username: HDembinski  
> Created at: 2020-05-03 15:25:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-623126874  

I still have to implement the change so that this can be done with the indexed_range, when that is ready, I will add an example and a note in the user guide.

---

## Comment 8

> Username: chiarazampolli  
> Created at: 2020-05-04 14:31:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/277#issuecomment-623499597  

Hello,  
Many thanks! For now, the implementation that you suggested works very well. When ready, I will try the one with indexed_range.  
Chiara
