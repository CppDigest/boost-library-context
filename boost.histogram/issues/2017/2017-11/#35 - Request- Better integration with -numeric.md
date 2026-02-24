# #35 - Request: Better integration with <numeric> [Closed]

> Username: HDembinski  
> Created at: 2017-11-20 10:29:43 UTC  
> Updated at: 2018-03-10 19:29:37 UTC  
> Closed at: 2018-03-10 19:29:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/35  

On 18. Nov 2017, at 17:08, Bjorn Reese via Boost <boost@lists.boost.org> wrote:  
  
I would like to see a better integration between boost.histogram and  
standard algorithms, especially those from `<numeric>`. Today they do not  
work together because the dereference operator of the axis iterator  
returns a pair (index and content) rather than the content.  
  
An example of where standard algorithms could be useful is calculating  
the cumulative distribution of a one-dimensional histogram, which ought  
to be a simple as:  
  
 auto& axis = h.axis();  
 std:partial_sum(axis.begin(), axis.end(), axis.begin());  
  
Another example is ranking a set of histograms based on the cosine  
similarity [1] using std::inner_product().  
  
[1] https://en.wikipedia.org/wiki/Cosine_similarity

---

## Comment 1

> Username: veprbl  
> Created at: 2017-11-20 17:28:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/35#issuecomment-345767909  

It would be an overstatement to say that it completely doesn't work:  
```  
std::vector<T> output;  
std:partial_sum(axis.begin(), axis.end(), std::back_inserter(output), [] (const pair &p1, const pair &p2) { return p1.second + p2.second; });  
```  
Yes, it is less convenient, and one could provide an interface for iterating only over the values.  
  
I see that the real problem is that example code inserts values back into the original histogram. This really feels like what ROOT users normally do. The ROOT developers and community have implemented all imaginable operations on histograms, so it made it very tempting to use them as you normally use, say, numpy arrays (I call this a *histogram driven data analysis*). I can imagine I don't need to tell how ugly it gets. And it applies to this case too. The cumulative distribution is not a histogram, it has a different meaning, you won't be able to safely manipulate it with routines designed to handle histograms (e.g. think how the bin statistics are not independent now). So my point is that a good histogram object should provide a means to reduce it to some other kind of value (a single number, or vector, or matrix, graph, ...), but list of operations allowed to produce a histogram should be limited to the ones that actually produce a sensible histogram.

---

## Comment 2

> Username: HDembinski  
> Created at: 2017-11-21 14:25:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/35#issuecomment-346042074  

> I see that the real problem is that example code inserts values back into the original histogram.  
  
I absolutely agree with you. A histogram is not a general containers of numbers, the values inside have a strong meaning. Giving write-access to the values would potentially violate the invariants of the class, notably the consistency of the variance estimates.  
  
I will quote my answer to the boost mailing list. tl;dr: I think I should provide a conversion to Boost.MultiArray. Users can then use iterators of `multi_array`. The interface could look like this:  
  
```  
auto h3 = /* some 3d histogram */  
// return copy of internal values as 3d multi_array, using value_type of histogram  
// (which derives from storage policy); throws if dimension is wrong  
auto array1 = h3.values<3>();  
// return copy of internal values using custom value type int, throws if dimension is wrong  
auto array2 = h3.values<3, int>();  
// for experts, try to return a read-only view of internal values as  
// const_multi_array_ref<3, int>; throws exception, if counts cannot be viewed like this;  
// maybe use boost::optional?  
auto array_view = histogram.values_view<3, int>();  
```  
  
> I think we need several iterators then. The current axis iterators iterate over the bins definition, not really over the bin content of the histogram itself. I chose the current iterators in this way, because I believe this scheme generalises well to the multi-dimensional case. I wouldn't replace these iterators, because they seem useful to me, but additional iterators that iterate over the bin values and play well with algorithms are also clearly needed.  
>   
> How should we define such iterators so that they generalise to multiple dimensions? For example, it would be easy to implement new iterators which just iterates over the bin content:  
>   
> auto h = make_static_histogram(axis::regular(3, 0, 1));  
> // fill […], then  
> std::vector<double> cumulative;  
> std::partial_sum(h.begin(), h.end(), cumulative.begin(), std::back_inserter(cumulative));  
>   
> Questions:  
> - What should this do if h is 2d? The same? If yes, in which order should the 2d array be iterated over?  
>   
> The current design keeps the internal order an implementation detail (I use column-major internally, because it is easier to compute the strides on the fly), the two choices are fortran-like (column-major) or C-like (row-major). Perhaps provide both with clear names so it is obvious? Or just row-major, because that's the standard in C++? These questions must have been already discussed during the design of Boost.MultiArray. I think I should provide a way to provide a MultiArray copy of the bin content, thus leveraging all the work that already went into defining MultiArray. Similarly one could provide a read-only view of the variance estimates. A read-only MultiArray view of the internal bin counts of the histogram is also possible for array_storage policy, but not for the default adaptive_storage policy.  
>   
> - Writable iterators?  
> In your example, you use writable iterators, to change the content (and the meaning of the content) of the histogram itself. In my reply here, I use read-only iterators, because changing the content of the histogram seems to go against the semantics. A histogram is more specific than a general container of values, the content has a strong meaning that cannot be arbitrarily changed. To be precise, what should the variance estimate, returned by `histogram::variance(…)` be for a cumulative count? The variance estimate for cumulative count is a whole matrix of covariances, since the successive entries are not independent. There are other operations where it is not clearly defined how the variance estimate should be computed. I think the best way to avoid these problems is to provide read-only iterators.

---

## Comment 3

> Username: HDembinski  
> Created at: 2017-11-23 17:02:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/35#issuecomment-346666735  

Ok, I thought about it some more. Returning a Boost.MultiArray does not work very well for several reasons. I don't want to return a copy, because the copy could be very large. Returning a view is difficult. Manually specifiying the histogram dimension in the call is also annoying.  
  
And if you just want to iterate over the values, it should be possible, so I am thinking of adding iterators for that. The code should then look like this:  
  
```  
auto h = /* some histogram */  
std::vector<double> csum;  
std::partial_sum(h.values().begin(), h.values().end(), std::back_inserter(csum));  
```  
  
The iterator just goes over all normal bins, excluding under/overflow bins in implementation-defined order. For 1d-histograms it would just work, and if you need to compute something multi-dimensional, you will be able to query the index of the current element. Like so:  
  
```  
auto h = /* some 2d histogram */  
for (auto iter = h.values().begin(); iter != h.values().end(); ++iter) {  
   std::cout << iter.idx(0) << " " << iter.idx(1) << " " << *iter << std::endl;  
}  
// prints:  
// 0 0 3.0  
// 1 0 2.0  
// ...  
```

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-03-10 19:29:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/35#issuecomment-372058404  

has been fixed mean-while in the master
