# #69 - Mergeable histograms? [Closed]

> Username: thvasilo  
> Created at: 2018-08-16 22:07:40 UTC  
> Updated at: 2018-08-21 03:05:28 UTC  
> Closed at: 2018-08-21 03:05:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/69  

Hello,  
  
First thanks for the library, it's great to have a fast header-only library for histograms in C++.  
  
One of the main use-cases we have for histograms is using them over distributed datasets, and then merging the results. Mergeable summaries are a big topic in CS, and there have been a number of papers on them (see this [paper for a review](http://www.weizhewei.com/papers/tods13.pdf))  
  
I'm wondering if there any plans for this library to also provide mergeable histograms in the future.  
  
Regards,  
Theodore

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-08-17 09:07:24 UTC  
> Updated at: 2018-08-17 09:07:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/69#issuecomment-413805398  

Hi, thank you for your comment.  
  
I looked into the abstract of your paper you referenced, but it is a bit too abstract to understand quickly what it is really about. You can sum histograms and the error estimates are correctly computed, all this is described in the documentation. If you want other kinds of "merging", you need to be more specific, please.

---

## Comment 2

> Username: thvasilo  
> Created at: 2018-08-17 17:29:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/69#issuecomment-413935293  

Paraphrasing from [Yahoo DataSketches](https://datasketches.github.io/docs/TheChallenge.html):  
  
The input data can be partitioned into many fragments. At query time each partition is processed with its own sketch (histogram). Once all the sketches have completed their scan of their associated data, we merge the sketches, and the resulting sketch (in this case histogram) should be representative of the complete dataset.  
  
Here's an example implementation of such a histogram for Python, based on "A streaming parallel decision tree algorithm" by Ben-Haim et al. : http://histogrammar.org/python/0.7.1/histogrammar.primitives.adaptivebin.AdaptivelyBin.html  
  
This particular histogram algorithm has also been implemented (AFAIK) in Go, Scala, and Java from others.

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-08-20 07:53:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/69#issuecomment-414229028  

> The input data can be partitioned into many fragments. At query time each partition is processed with its own sketch (histogram). Once all the sketches have completed their scan of their associated data, we merge the sketches, and the resulting sketch (in this case histogram) should be representative of the complete dataset.  
  
I am still not sure from that description what should happen, perhaps you could give me an example? The actual mathematical operation of "merging" is not explained. I can think of at least two different things. One is adding histograms with the same layout. You can do that already. The other thing is stitching histograms together. Let's say you have one histogram with a range 0 ... 1 and another with 1 ... 2, then you could stitch the two together to get one histogram with range 0 ... 2. At first glance, I cannot see a use-case for such a feature, but it is possible to add something like that.  
  
> Here's an example implementation of such a histogram for Python, based on "A streaming parallel decision tree algorithm" by Ben-Haim et al. : http://histogrammar.org/python/0.7.1/histogrammar.primitives.adaptivebin.AdaptivelyBin.html  
  
AdaptivelyBin is an automatic binning strategy, where the number of bins is not fixed, but an algorithm tries to select a binning that represents the data distribution in efficient way. I would not call this "histogram merging". Automatic binning is beyond the scope of this library. Automatic binning is extremely difficult to implement in higher dimensions, when you look around, you will only find algorithms for 1-D data for a reason. If you want automatic binning for one-dimensional data in C++, you can use Boost.Accumulators, they have a density estimator with automatic binning. I personally like the Bayesian Blocks algorithm for automatic binning, but again it is for 1-D data only.

---

## Comment 4

> Username: thvasilo  
> Created at: 2018-08-21 03:05:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/69#issuecomment-414534902  

Yup the histogram I linked has adaptive binning for the creation of the histograms, then another algorithm for merging such histograms. Since this library is focused on multi-dimensional histograms I guess this answers my question.
