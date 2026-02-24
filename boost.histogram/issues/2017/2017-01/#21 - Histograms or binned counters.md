# #21 - Histograms or binned counters [Closed]

> Username: veprbl  
> Created at: 2017-01-07 02:38:04 UTC  
> Updated at: 2017-08-30 07:25:23 UTC  
> Closed at: 2017-08-30 07:25:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/21  

Hi! This is a really great work! A clear improvement over TH* mess.  
  
There is another library which attempts to tackle this issue - [YODA](http://yoda.hepforge.org). They have some interesting ideas and one of them is that they've realised that ROOT histograms are not exactly histograms but more of "binned counters". A simple illustration of this can be done using modified 1d histogram example:  
```python  
import histogram as hg  
import numpy as np  
import matplotlib.pyplot as plt  
  
h = hg.histogram(hg.variable_axis(1.,2.,4.))  
h.fill(1.5)  
h.fill(2.5)  
  
x = np.array(h.axis(0))  
y = np.asarray(h)  
y = y[:len(x)-1]  
y = np.append(y, [0])  
  
plt.plot(x, y, drawstyle="steps-post")  
plt.ylim(0.5, 1.5)  
plt.xlabel("x")  
plt.ylabel("y")  
plt.show()  
```  
This will draw a straight line suggesting that the probability of filled value is uniformly distributed over range from 1.0 to 3.0, which is a wrong estimate. YODA, on the other hand, operates with values normalised by the bin width by default, so it will draw a step instead of a straight line. I think it makes a lot of sense for boost_histogram to distinguish between bin width and sum of weights as well.

---

## Comment 1

> Username: HDembinski  
> Created at: 2017-01-08 17:14:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/21#issuecomment-271164434  

Hi!  
Thank you for the nice feedback! I am still working on getting this library into boost. Please also check out the development version in branch "static", which features an even faster static_histogram type for C++ (you cannot use it from Python, though) and a more consistent interface on the C++ side.  
  
Now to your point about histograms being binned counters. I know what you mean, but for technical reasons, the histogram has to store counters per bin internally. It is not safe to store densities directly, and it would seriously harm the performance of high-dimensional histograms.  
  
However, on the C++ side it would be easy to add a .density(index) member, which would return the count in the bin divided by the bin width. On the Python side, the could be a .as_density attribute, which would generate a numpy array of densities on the fly from the stored counts.   
  
I will add that to the static branch.

---

## Comment 2

> Username: HDembinski  
> Created at: 2017-01-08 18:35:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/21#issuecomment-271169435  

PS: Wikipedia on histograms: "In a more general mathematical sense, a histogram is a function ... that counts the number of observations that fall into each of the disjoint categories (known as bins)." This is in line with the way ROOT and this library model histograms.
