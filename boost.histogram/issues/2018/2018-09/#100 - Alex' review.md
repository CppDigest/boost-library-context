# #100 - Alex' review [Closed]

> Username: HDembinski  
> Created at: 2018-09-21 10:24:12 UTC  
> Updated at: 2018-12-21 07:43:45 UTC  
> Closed at: 2018-12-21 07:42:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/100  

I like the histogram library because it offers a straightforward solution to a common problem. In my experience it has never been a complex problem (because of the size and complexity of my data), but still it is nice to have a clean solution, avoid duplication, and avoid pitfalls related to under/overflows, and open/closed intervals.   
  
From that perspective, I would really like the library to be as intuitive and robust as possible.  
  
Based on that I have a couple of questions / remarks  
  
*Axis_type concept (should probably be AxisType)*  
  
Why must axis_type derive from labelled_base and iterator_mixin?   
  
My understanding is that concepts are ideally specified in terms of usage and behavior, rather than implementation. So for iterator_mixin, would it not be better to just require that the following works (with the usual semantics)  
  
auto i = axis.begin();   
auto i = axis.end();  
auto i = axis.rbegin();  
auto i = axis.rend();  
  
You need to document what iterator concept you adhere to, e.g. I would promise/require OutputIterator with additional requirement of multipass.  
  
Likewise for labelled base can you just require:   
  
boost::string_view str = axis.label();  
axis.label(str);  
auto a = axis.get_allocator(); // I don't really see the point of this to be honest  
  
and for base:  
  
int a = size();  
int b = shape();   
int c = uoflow();   
  
shape() and uoflow() are not very intuitive names/functions to me, how about bool has_underflow() and bool has_overflow() to get the same information.   
  
As a user implementing my own axis type, I would prefer just implementing those ten member functions over deriving from the two base classes. It would allow me to make an axis-type that is independent of boost / boost::histogram, and avoid complex inheritance. And, if I wanted, I could still derive from the base classes.  
  
Further to those, you would just have   
  
auto b = axis[i];// where b is of type axis::bin_type  
auto i = axis.index(v); // where v is type axis::value_type  
  
For these you need to document how the under- and overflow bins are numbered  
  
For the AxisType concept definition to be complete, you must also define a BinType concept for the bin_type and a SortableValue concept (I suppose) for the value_type.  
  
Why did you chose to make the label a part of the  axis? Of course there are many cases where we like the axis to have a label, but is that not true for many other classes, for instance std::vector, std::map, etc. Making this a default seems to be against the stated idea of "you don't pay for what you don't need". It seems to me that you require me to use a pair<axis, string> where I would be happy to use an axis.  
  
Why is it necessary for axes to be comparable? And is is not asking for trouble to also compare the axis title (considering lower/upper case typos, etc.)   
  
*Not documenting the histogram class*  
  
I could not find the documentation/reference for the histogram class. I suppose this is on purpose because there are two implementations that have a common interface? In that case I would document the common interface as a concept too.  
  
The user should then in my opinion also not use the histogram class directly, but strictly rely on your factory functions that create Histogram (the concept, not the class) objects.  
  
In the tutorial there is the following part to create histogram by copying or moving axes from a std::vector of axes.  
  
auto h1 = make_dynamic_histogram(v.begin(), v.end()); // copying axes  
auto h2 = bh::histogram<decltype(v)>(std::move<v>); // moving axes  
  
Now, h1 seems very reasonable to me, but for h2 you now need to use the undocumented histogram class. It is not clear to me what the two template arguments are for in histogram<A,S>. It strikes me as strange that A should be a vector<axis::any_std>, because having the axes stored in a vector seems to be an implementation detail irrelevant to the user.  
  
Perhaps this could be avoided by providing factory functions, that take a Range of axes instead of pairs of iterators. e.g.  
  
template<typename AxisRange> make_dynamic_histogram_from_range(AxisRange&& axes);  
template<typename Storage, typename AxisRange> make_dynamic_histogram_from_range(Storage&& storage, AxisRange&& axes);  
  
as this is Perfect Forwarding, you can automatically decide whether to move or copy. Furthermore, the user is not limited to providing axes in a std::vector   
  
*Not having an overflow bin for Circular axes*  
  
How can this type of axes deal with NaN input?  
  
*Fill Histogram*  
  
The functional style example seems overly complicated and apparently (according to your comments) depends on smartness of the compiler, would the following not be preferred?   
  
auto h2 = make_static_histogram(  
 bh::axis::regular<>(8, 0, 4),   
 bh::axis::regular<>(10, 0, 5));  
for(auto&& v : input_data) h2(v);  
  
I know this is not strictly functional, but you could easily provide another factory function to do this (i.e. something like "make_and_fill_histogram").   
  
*Access bin counts*  
  
It seems unfortunate that the methods to access the indices of a bin are methods of the iterator rather than the value. This would preclude most uses of range-based for-loops with histograms. I see that to change this could be tricky, depending on the  iterator concept you wish to support. Perhaps it is an option to provide separate Ranges (Views) for histograms that iterate only over values; over values and variances, over indices and values and over indices, values and variances.  
  
You could have something like this:  
  
for(auto&& v : h)     // alternatively: for(auto&& v :  indices_and_values_view(h))   
{  
 std::cout << v.idx(0) << ' ' << v.idx(1) << ' ' << v.value() << std::endl;  
}  
  
Instead of:  
  
for (auto it = h.begin(); it != h.end(); ++it)   
{  
 std::cout << it.idx(0) << ' ' << it.idx(1) << ' ' << it->value() << std::endl;  
}  
  
*Functionality*  
  
The documentation says that automated bin boundaries are not an option, because there is no consensus on how to do this. While I understand you would not want to take this on, I would think a library that includes a number of methods to do this with the possibility to add your own would be very valuable and lack of consensus is no real argument. For instance, GIS packages do this to create legends for geographical maps, and I would like to have this capability at my fingertips.   
  
It would be useful to be able to aggregate bins with an intuitive interface, e.g. if I have the population age distribution in 1-year intervals, aggregate it to 5-year intervals. This would be a generalization of the reduce_to function.  
  
I like that you included the option to combine storages. Have you also considered the option to subtract storages / samples? This would make histogram more suitable for moving window type analysis.   
  
*Conclusion*  
  
I vote for inclusion in Boost because it offers fundamental and very useful functionality.  
  
- What is your evaluation of the design?   
-- More can be done to simplify concepts  
-- More can be done to simplify the interface, especially to support use with range-based for-loops.  
-- I think the AxisType concept would be better without the label  
  
- What is your evaluation of the implementation?  
-- Didn't look in detail. To me it seemed more complex than necessary, but perhaps that is where the good performance comes from.  
  
- What is your evaluation of the documentation?  
-- Good, could always be better. It is enough to get started. Some important parts are still missing   
  
- What is your evaluation of the potential usefulness of the library?  
-- I would use it  
  
- Did you try to use the library? With what compiler?  
-- No  
  
-  Did you have any problems?  
-- No  
  
- How much effort did you put into your evaluation?  
 A glance? A quick reading? In-depth study?  
-- A careful reading, and checking in code where I did not understand docs  
  
- Are you knowledgeable about the problem domain?  
-- Not really  
  
*Spitting of coffee*  
  
The documentation is the first impression people will get from a library. It is incredibly generous of reviewers to pick through the language and give detailed general and specific advice. Avoiding the passive tense where possible is good and useful advice.

---

## Comment 1

> Username: jpivarski  
> Created at: 2018-09-21 12:35:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/100#issuecomment-423516054  

Automated binning is one of the things that silently divides particle physics histogram users from those in other fields. Most of our histogram packages from HBOOK to ROOT provide mainly or only fixed binning options while those in other packages have automated binning as a default. A Boost library should serve more than particle physicists, but I'm at a loss for how to do it in a way that would make everyone happy.  
  
The main reason we don't want automated bins in particle physics is because our datasets almost always don't fit in memory and we want to minimize the number of passes over the data. By setting fixed bins we can fill in one pass and parallelize over distributed data, confident that we can simply add the results. I don't know of any automated binning algorithm that can set bin boundaries from a subsample and then reconcile the different bins from different samples without approximation. If anyone has any ideas for this, I'd like to hear them!  
  
A secondary reason is for experimental stability: we want to produce directly comparable results from different runs of an experiment. That should be universal, though.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-09-24 09:31:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/100#issuecomment-423920117  

I think we should support basic simple single-pass algorithms, like the one hat simply extends the axis with a constant bin width by more bins. If the user needs a multi-pass algorithm, then (s)he should use a separate code to compute the bin boundaries.  
  
Algorithms which compute bin boundaries are only loosely coupled to any specific histogram implementation, I think. If that's the case, it is actually better if they are not integrated in boost.histogram.

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-12-21 07:42:55 UTC  
> Updated at: 2018-12-21 07:43:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/100#issuecomment-449293558  

I am closing this, because I believe all the concerns have been addressed, except for the following things, which have their own open issues:  
- overhaul of the concept documentation, including the axis and bin type concepts and description of optional parts  
- axis that can grow, as a minimal form of automatic binning  
- make histograms subtractable
