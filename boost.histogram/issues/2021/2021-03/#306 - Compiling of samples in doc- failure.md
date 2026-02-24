# #306 - Compiling of samples in doc: failure [Closed]

> Username: skramm  
> Created at: 2021-03-05 23:56:08 UTC  
> Updated at: 2021-03-06 17:30:43 UTC  
> Closed at: 2021-03-06 17:30:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/306  

Hi,  
First, congrats for this great lib that I already use for basic 1d histograms.  
  
I am currently experimenting with custom accumulators and I am facing a build issue. So back to basics, I tried to build the provided sample code. And no success!  
[See here](https://www.boost.org/doc/libs/1_75_0/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_accumulators), second example:  
I juste pasted that, and got:  
```  
main.cpp:11:57: error: expected template-name before '<' token  
   11 |   struct accumulator_with_metadata : accumulators::count<> {  
main.cpp:11:57: error: expected '{' before '<' token  
main.cpp:11:57: error: expected unqualified-id before '<' token  
```  
[See it here on Coliru](http://coliru.stacked-crooked.com/a/5687dd4f0b6b56d7).  
(This is with C++17, I try to stick to C++14 and got the same message)  
  
  
Any idea?

---

## Comment 1

> Username: henryiii  
> Created at: 2021-03-06 01:04:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/306#issuecomment-791822613  

Don't you need a keyword like "public" there for inheritance?

---

## Comment 2

> Username: skramm  
> Created at: 2021-03-06 08:18:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/306#issuecomment-791894487  

I think it got optional/default at some point (C++11?), but anyway, I tried with  
`struct accumulator_with_metadata : public accumulators::count<> {`  
same problem.

---

## Comment 3

> Username: henryiii  
> Created at: 2021-03-06 16:58:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/306#issuecomment-791989372  

Sorry, was on my phone and was just looking for possible issues. Yes, "private" is the default, which I rarely find useful, so I'd forgotten it worked.  
  
I think the issue here might be that Coliru using an old version of Boost that doesn't have this accumulator. I don't seem to be able to include `#include <boost/histogram/accumulators/count.hpp>`. If I try the exact same code on Wandbox [link](https://wandbox.org/permlink/deIvzP7VCVeSmDg9), it works just fine (and I can include the above file explicitly if I want to, as well).  
  
Pretty sure that's the problem.

---

## Comment 4

> Username: henryiii  
> Created at: 2021-03-06 17:02:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/306#issuecomment-791990169  

Just checked, Coliru uses Boost 1.71.0. Current version is 1.75.0, and Boost.Histogram was introduced in 1.70.0 (IIRC, +/- 1), so that's a pretty early version for Boost.Histogram. Some things available now should be expected to be missing back then.

---

## Comment 5

> Username: skramm  
> Created at: 2021-03-06 17:30:28 UTC  
> Updated at: 2021-03-06 17:30:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/306#issuecomment-791994896  

Ah, well ok, thanks, I should have checked that, sorry for the noise!  
I'll try to upgrade my machines boost version.
