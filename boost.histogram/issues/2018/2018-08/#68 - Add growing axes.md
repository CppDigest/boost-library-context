# #68 - Add growing axes? [Closed]

> Username: HDembinski  
> Created at: 2018-08-07 13:05:42 UTC  
> Updated at: 2019-01-16 20:04:15 UTC  
> Closed at: 2019-01-16 20:04:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/68  

The experimental ROOT histograms have axes that can grow.  
https://root.cern.ch/doc/master/classROOT_1_1Experimental_1_1RHist.html  
https://root.cern.ch/doc/master/classROOT_1_1Experimental_1_1RAxisGrow.html#abdd1c014e04380d452d17e362d5df1fb  
  
Basically, one defines a number of bins and an initial range, which defines the step size. When a value is out of bounds, the range is multiplied by an integer while the number of bins is kept constant. This multiplies the step size. Neighboring bins are merged in the process in accordance with the new definition.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-10-23 10:17:57 UTC  
> Updated at: 2018-10-23 10:18:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432187882  

Instead of an integer index, a growing axis should return a `std::pair<int, int>`, where the first argument `i` is the index and the second argument `k` indicates whether the axis should be extended at the front or the back. If `k<0`, extend the axis by `abs(k)` at the front. If `k > 0`, extend the axis by `k` at the back.

---

## Comment 2

> Username: jpivarski  
> Created at: 2018-10-23 13:41:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432251564  

This can also be seen as a step toward sparse histograms. Sparse histograms also have to grow, but additionally hold explicit bin indexes because bin index is not determined by position. I would say that a growable axis is all you need for sparse histograms, but for fast lookup, they should probably also be sorted by bin index, so the growth would have to be able to insert between existing bins as well as below and above all other bins. I suppose that's out of scope for this issue, but you might want to keep it in mind because you might find a common solution for both growable and sparse axes.

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-10-23 14:00:49 UTC  
> Updated at: 2018-10-24 13:14:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432258972  

Sparse histograms are decoupled from this, no? It would be possible already now to have a sparse histogram with an appropriate storage backend. I will add a traits class that will allow users to plug in a normal std::map or std::unordered_map as the backend, giving you a sparse histogram.  
  
I am very skeptical about the practical usefulness of sparse histograms. The size overhead for each non-zero bin is substantial, usually more than a pointer, so 64+ bits. You only need sparsity when you have very fine binning, but that also means you have many non-zero bins even if there is only one entry per bin.  
  
But we will see. Adding sparsity is easy thanks to the flexibility of the library, so I won't stand in the way of people who want to try.

---

## Comment 4

> Username: jpivarski  
> Created at: 2018-10-23 14:41:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432275328  

Just now, I was thinking of sparse histograms with a fixed bin-width (and offset from origin, which is usually 0 but might be -0.5 for integers). Like a `regular` axis, floating-point positions are converted into integer indexes by dividing by bin-width, but unlike a dense `regular` axis, a corresponding container might not already exist. The storage cost for a bin is the size of that integer (32 or 64 _bits_) plus the container (another 32 or 64 bits). Generally, the storage cost for this kind of sparse histogram is double that of a dense histogram with the same number of non-zero bins.  
  
In histbook, this is called `groupbin`, though the storage costs are much worse because I use a Python dict as a growable container.  
  
Although high-dimensionality histograms are usually cited as use-cases for sparse histograms, what I have in mind is histogramming a mostly well-behaved distribution with a few extreme outliers. I remember encountering this problem very often in HEP: we know that the "X" distribution is in the ballpark of 100-200 units, but we want to let the histogram range float because we don't know exactly where it trails off (and we don't want to run through all the data with a high edge of 300 only to find that we need to set it higher and run again). The actual maximum of the distribution is 1.7e315 because of some funny calculation upstream— it happens all the time. So if we let the histogram choose its own binning, it will be way off. Instead, we'd like to make the bin-width something like 5 and have it fill all the non-zero bins. Bins up to 350 are filled with the distribution we care about it and a few wacky bins at 1.7e315 are also filled, which we can safely ignore.  
  
A growable axis would be a disaster in this particular case because it would try to extend, densely, all the way up to 1.7e315.  
  
I brought this up here because I thought it was the first instance of non-fixed binning. It's not clear to me how `std::map` storage could implement sparsity now because I don't see how a never-before-seen bin value would create a new initialized storage container: it seems to involve some cooperation between the axis type and the storage type, whereas I thought these were independent in the design of the library.  
  
As a simpler problem than `groupbin` (fixed bin-width, new bin indexes spawn new bins), do you mean to say that it's right now possible to implement a `groupby` (bins are strings; new strings observed in the data spawn new bins, to make a standard bar chart)? When we last talked, I thought you said that it wasn't possible: the set of strings would have to be fixed before filling.

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-10-24 08:16:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432558251  

> The storage cost for a bin is the size of that integer (32 or 64 bits) plus the container (another 32 or 64 bits). Generally, the storage cost for this kind of sparse histogram is double that of a dense histogram with the same number of non-zero bins.  
  
It is way larger. https://stackoverflow.com/questions/720507/how-can-i-estimate-memory-usage-of-stdmap  
  
For each item, you pay for the key, the value, and the element overhead to implement a binary tree, which is two pointers. On a 64 bit machine, this is 4 * 64 = 256 bits per non-zero item. If you use adaptive_storage, it is 8 bits for every item. You need a fill-factor of less than 1/32 to beat this. There is no need to store pointers and the key does not cost you anything either.

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-10-24 08:32:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432563469  

> I have in mind is histogramming a mostly well-behaved distribution with a few extreme outliers. I remember encountering this problem very often in HEP: we know that the "X" distribution is in the ballpark of 100-200 units, but we want to let the histogram range float because we don't know exactly where it trails off (and we don't want to run through all the data with a high edge of 300 only to find that we need to set it higher and run again). The actual maximum of the distribution is 1.7e315 because of some funny calculation upstream— it happens all the time. So if we let the histogram choose its own binning, it will be way off. Instead, we'd like to make the bin-width something like 5 and have it fill all the non-zero bins. Bins up to 350 are filled with the distribution we care about it and a few wacky bins at 1.7e315 are also filled, which we can safely ignore.  
  
If you don't use automatic binning, the over- and underflow bins capture these outliers.

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-10-24 08:50:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432570515  

> I brought this up here because I thought it was the first instance of non-fixed binning. It's not clear to me how std::map storage could implement sparsity now because I don't see how a never-before-seen bin value would create a new initialized storage container: it seems to involve some cooperation between the axis type and the storage type, whereas I thought these were independent in the design of the library.  
  
Sparsity works already with non-growing axis types. The histogram host class computes a linear index from the axis indices, e.g. let's say we have three axes which produce a 3d index of (1, 2, 3). Let's say this is mapped to a linear index of 10. A histogram starts with an empty std::map. Now the map is called with key 10 and produces a new item with an empty bin count of 0, which is then immediately increased by 1. The map only grows with each non-zero bin.  
  
A growing axis is a serious challenge for my design, since the bin values need to be copied around when the addressing scheme changes after an axis changed size. Currently,  If the ranges of an axis is modified, the computation of the linear index changes, and (1, 2, 3) may now be mapped to 12 instead of 10, for example. This means that the counter value at index 10 needs to be moved to index 12, in fact all counter values need to move.

---

## Comment 8

> Username: HDembinski  
> Created at: 2018-10-24 08:56:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432572953  

> As a simpler problem than groupbin (fixed bin-width, new bin indexes spawn new bins), do you mean to say that it's right now possible to implement a groupby (bins are strings; new strings observed in the data spawn new bins, to make a standard bar chart)? When we last talked, I thought you said that it wasn't possible: the set of strings would have to be fixed before filling.  
  
It is currently not possible, because I don't have growing axis types. Currently, the axis has to know already at construction how large it is, meaning how many bins it has. Strictly speaking, the association between bin value and bin index could be determined later. So it would be possible to make a variant of `axis::category` where you only specify that there are four distinct string labels, but don't say upfront what they are and let the axis discover them as data is filled. However, once you encounter a fifth distinct string, it will be counted in the overflow bin.

---

## Comment 9

> Username: jpivarski  
> Created at: 2018-10-24 11:28:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432618829  

I'll think about efficient growing data structures offline, then. In Scala's `Vector` class, they found an optimum between building a tree down to each element vs copying data to insert— the final data structure was a hybrid that built a tree down to fixed-size panels. The binary tree overhead is only felt per panel and the time cost of copying data is only felt within each panel.  
  
The ROOT documentation also describes their `THnSparse` data structure in low level terms.  
  
If I come up with something or decide that what ROOT does is best, I'll try implementing it in your axis/storage abstraction.

---

## Comment 10

> Username: HDembinski  
> Created at: 2018-10-24 13:16:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-432651676  

Cool, please do! 👍

---

## Comment 11

> Username: HDembinski  
> Created at: 2019-01-16 20:04:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/68#issuecomment-454920295  

implemented in develop branch
