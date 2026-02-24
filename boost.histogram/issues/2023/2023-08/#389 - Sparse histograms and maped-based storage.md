# #389 - Sparse histograms and maped-based storage [Open]

> Username: btovar  
> Created at: 2023-08-23 18:14:37 UTC  
> Updated at: 2025-04-22 09:32:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/389  

From [this post](https://root-forum.cern.ch/t/boost-1-70-released-new-library-boost-histogram/33595/4) from some years ago, there seems to be the implication that having a sparse histogram should be feasible using boost-histograms. Is there a map-based storage supported out of the box that we could use, or that would be easy to implement to target the scikit-hep python bindings?  
  
For some context, we implemented some histograms using scikit-hep hist. These histograms have a handful of categorical axes, and ~300 regular/variable axes. The histograms are pretty sparse, with 97% of zeros of about a billion bins. In our HEP analysis, adding the results becomes impractical as they need > 120GB of memory.  Using a subclass of hist that internally uses a dictionary of histograms [categorical keys -> dense hist],  we were able to reduce the memory usage to about 20GB, which made the approach practical again in terms of memory and runtime. This subclass tries to mimic the h[...] access of the original hist, and uses awkward arrays where the original hist would return numpy arrays, etc. However, it is somewhat inefficient as the storage (in the sense of the function that tells which bin to update) is completely in python and has to use python dictionaries.  
  
Ideally, we are looking for a storage that would enable spare histograms that then can be used in scikit-hep's boost-histograms and hist. We would appreciate any guidance on this topic.

---

## Comment 1

> Username: HDembinski  
> Created at: 2023-08-30 09:09:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1698789320  

Yes, you can use any kind of map as a storage which supports the standard library map interface. Please see https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_storage_class  
  
The map should preferably be a hash map. This looks like a good candidate https://github.com/greg7mdp/parallel-hashmap, specifically phmap::parallel_flat_hash_map. I haven't tested it, though.

---

## Comment 2

> Username: btovar  
> Created at: 2023-08-30 11:44:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699004738  

Thanks @HDembinski, I'll give that a try!

---

## Comment 3

> Username: lgray  
> Created at: 2023-08-30 11:50:19 UTC  
> Updated at: 2023-08-30 11:50:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699016167  

I think more the point here is that this addition would be good to have as part of boost-histogram (or its bindings) so that we don't create a fine dust of packages. Lots of HEP users of this package and the python bindings would benefit from sparse storage.  
  
Given that the easiest route here probably doesn't fit in boost (given probably using parallel-hashmap), I guess we should open a PR extending https://github.com/scikit-hep/boost-histogram ?  
  
Thanks for the pointer to the parallel-hashmap, one thing we definitely want here is thread friendliness.

---

## Comment 4

> Username: HDembinski  
> Created at: 2023-08-30 12:06:11 UTC  
> Updated at: 2023-08-30 12:07:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699040126  

@lgray Yes, please add an issue to https://github.com/scikit-hep/boost-histogram. I agree that if there is a clear use-case for sparse storages, then we should support them in the Python package.  
  
@henryiii It should be easy to add a hashmap storage to boost-histogram, right?

---

## Comment 5

> Username: henryiii  
> Created at: 2023-08-30 15:25:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699392375  

Adding it should be pretty easy. The only tricky part on the C++ side is setting up the conditional parts for functions that have really different returns when using this sparse storage. Also this will increase the compile complexity - how many backends would be set up for sparse storage? `weighed_sum`, or all of them? The hard[^1] part, though, is getting a view of the data in Python. I'm not sure what the standard for sparse array support in Python is currently, other than it's being worked on. (Also, making sure we support setting the values from Python might be a bit tricky.)  
  
None of this is insurmountable, it's just something that I don't currently have time to work on, but I'd be able to help someone work on it (like @btovar). I could possibly put it forward as a Fellows project, as well.  
  
[^1]: Hard meaning it requires research, at least it would for me, on what the best method is. Probably not hard to implement.

---

## Comment 6

> Username: HDembinski  
> Created at: 2023-08-30 16:07:26 UTC  
> Updated at: 2023-08-30 16:09:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699459239  

Some spontaneous thoughts on the implementation in boost-histogram (Python lib).  
  
- With a sparse map, we are cannot provide a memory view into the sparse storage, as we do with dense storage.  
- The accessors `values()`, `variances()` etc. could be implemented so that they return a Python dict, which would decouple the internal implementation of the sparse map from the Python world. This seems like the Pythonic thing to do. The caveat is the data is copied in the process, and one would not be able to modify the histogram content via assigning to `values()` etc.  
- A more advanced step (could be done in a later iteration) would be to wrap `phmap::parallel_flat_hash_map` to Python and give it a `dict`-like interface. This would allow us to return the data of the histogram without making copies and allow one to modify its state.

---

## Comment 7

> Username: btovar  
> Created at: 2023-08-30 16:36:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699507042  

Accessing the data without copying may be a requirement given memory constraints. However, if we can process one pair of  (key, value) at a time it may work.  
  
Could the storage of one boost_histogram be a boost_histogram-like structure?  E.g., have the first histogram be sparse categorical axes, and have its storage be a dense histogram. In python we could then return dictionaries that transparently show the dense data. There would be need to overload functions like h.axes so that it reflects the second histogram, etc.

---

## Comment 8

> Username: henryiii  
> Created at: 2023-08-30 16:46:17 UTC  
> Updated at: 2023-08-30 16:58:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699521280  

The accessors (`values()`, etc) must return dense values, since they are meant to be passed to plotting libraries and such, and are unified across all storages. A user doesn't have to know what storage they have to use these, that's the point of them over `.view`. There already is no guarantee that the values they return can be modified. Some of them already can't be. `values()` I think always does avoid a copy, currently, but that's an implementation detail; no one should be setting `h.values()[...]` and we might (or should?) make these read-only[^1].  
  
I think `.view()` should return whatever the current standard for sparse storages in Python is. That one is storage specific, unlike the UHI accessors. I think that's [`scipy.sparse`](https://docs.scipy.org/doc/scipy/reference/sparse.html#sparse-array-classes) something, probably `coo_array`. It sounds like it's not complete, but being worked on. There's a chance that this would require it's own copy of the data, though, in which case, maybe we'd have to make a custom dict-like viewer that does provide access to the original data, and then provide some sort of utility function or method to make a copy into the `scipy.sparse` format. (Also, we need weighed support, so that might be the best or even only way to go for weighted and/or mean storages.)  
  
We do need the ability to modify the state of the storage even for a first pass, I think. Unpickling, for example, has to make and fill a storage directly. We also have to make and fill storages for some indexing operations, etc.  
  
[^1]: I struggle with read-only's meaning. There's the "correctness" approach, which would be making anything that's a copy read-only, helping detect when a user makes a mistake assigning to something that won't modify the original. But then there's the "practical" approach, which says that forcing a user to make a copy of a copy to modify it is bad; this is already a copy, so a user should be able to modify it safely.

---

## Comment 9

> Username: henryiii  
> Created at: 2023-08-30 16:55:54 UTC  
> Updated at: 2023-08-30 16:56:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699534492  

> have the first histogram be sparse categorical axes, and have its storage be a dense histogram  
  
I don't understand exactly what this means. If you have 3 categories, each with a dense storage, then that's already what a dense overall storage with 3 categories is. Boost-histogram supports growing category axes. If you mean each of the "dense" histograms inside the categories is different, then this already sounds complicated for only a small benefit, and it would be better to just solve this with a fully sparse storage that would allow more than one "special sparse" axis.  
  
Hist does almost support this (if I understood what you were asking), by the way, with "Stack"s. It just doesn't support filling stacks and miss-matched categories on categorical axes, but that could probably be added. So if you are thinking about "one special" axes, I'd look at possibly adding this to Hist's Stack's.

---

## Comment 10

> Username: btovar  
> Created at: 2023-08-30 17:39:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-1699590168  

Yes, I was thinking of having different histograms as the storage, which is what we currently do in a class purely implemented in python. We use several categorical axes to index the dense parts by implementing `__getitem__` accordingly.  The advantage of doing it this way is that you can get the view() of the dense parts without copying (h.view() returns a dictionary where the values are the .view() of the inner histograms, `h.values()` for now returns an awkward array).  One issue is that the `__getitem__` is complicated and would need to keep track of the changes in hist.Hist code. Another issue is that `__setitem__` can only be used per one combination of categories (say `h["a", "b", "c"] = ...`), but I was able to make pickle work by writing the appropriate serialization functions.  
  
I looked at the histogram stacks, but indeed, there is no special categorical axes. (E.g. we may sum over any of the as needed.) I agree that solving it with a full sparse storage would be best.

---

## Comment 11

> Username: Superharz  
> Created at: 2025-04-15 08:49:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-2804312079  

Hello everybody,  
  
what is the status of the sparse implementation in boost histogram? I would like to use it because I am dealing with many dimensions and many empty bins (approx. 95% of the bins are empty).

---

## Comment 12

> Username: HDembinski  
> Created at: 2025-04-22 09:31:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/389#issuecomment-2820737508  

If you read this thread and the docs, you should be all set. Sparse Storage is supported.
