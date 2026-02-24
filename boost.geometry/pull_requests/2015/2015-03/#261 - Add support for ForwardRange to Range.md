# #261 Add support for ForwardRange to Range [Closed]

> Username: sdebionne  
> Created at: 2015-03-11 13:02:25 UTC  
> Updated at: 2015-03-12 08:27:46 UTC  
> Closed at: 2015-03-12 08:27:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/261  

Add support for ForwardRange to `pos()`, `at()`...  
Operations on RandomAccessRange remain O(1) while other Range operations are O(N).  
The ForwardRange support is tested with `sd::list<>`  
  
In my problematic use case, the range of interior rings of an adapted Polygon is a ForwardRange. I'm not sure what to expect in term performance penalty (the Ring itself is RandomAccessRange of Point), any clue ?  
  
All tests passed on Windows.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-03-11 14:22:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78272053  

The PR looks good, thanks for proposal!  
  
Though I'm not sure if we should allow using ForwardRanges exactly because of the performance degradation. I know that it was done like this in the STL (`distance`, `advance`) but a function silently changing the complexity for a different input is probably not a good idea. Esspecially if it's called `at()` which is a `vector`'s method. This brings some user's expectations. For such functions with variading complexity, instead of `at()` we should probably use something like `at_advanced()` name. AFAIR `boost::size()` also expects RandomAccessRanges. Probably because of the confusion It could cause, for this the user should use `distance`.  
  
On the other hand in the case of `erase()` instead of O(1) for a list as you'd probably expect the complexity would be O(N) as for `vector`, though maybe it could be "optimized" (with SFINAE or some ADL trick).  
  
Answering your question about the performance, it probably depends on the algorithm and the input data. I'm guessing that typically it shouldn't be a problem since Polygons hasn't many holes in general, probably. You should do some benchmarking to see how does it look like in your case.  
  
Maybe it'd be possible to tweak some algorithms and instead of storing indexes or along with indexes store iterator(s). This would be interesting.  
  
But the most important question is, couldn't you just use `deque` or `vector` instead? It seems surprising that you need `list` here. It'd in most cases be faster than `list`. Or maybe `boost::stable_vector` if the reference stability is what you need? Though I'm not sure how fast it is.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-03-11 17:10:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78308653  

I'm basically OK with this if Adam's questions are answered.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-03-11 18:23:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78338963  

I'm not really ok with it :)  
  
What I had in mind is that technically the PR is good, but still probably shouldn't be merged.  
`at()`, just like `size()` should expect RandomAccessRange.  
  
The more general question is, should we support ForwardRanges of SingleGeometries in Multi, Rings in Polygon and/or Points in Range? If the answer was yes, then we should probably support it "properly" in algorithms, i.e. use/store iterators instead of indexes, don't use `at()` or `size()`, etc. But I do realize that it's probably more work.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-03-11 18:31:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78340676  

I agree with your objections, and, I think, the answer is (for the foreseeable future): no, we should not support Forward ranges in algorithms - we use those indexes everywhere. It is a lot of work and for the moment we have other objectives.  
However, I did not oppose to this PR which looks good. But I understand your reasoning, this will give the impression that it is supported.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2015-03-11 20:41:34 UTC  
> Updated_at: 2015-03-11 20:43:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78366549  

@awulkiew I get your point, that the reason why I mentioned the complexity in my initial post. In the other hand, concept checking should trap ForwardRanges in places where it is really not acceptable (e.g. Points in Range).  
  
My only use case for ForwardRange is for interior rings of polygons. A better solution would be to relax this part of the polygon concept:  
  
> The Polygon Concept is defined as following:  
> - there must be a specialization of traits::interior_type defining the type of the collection of its interior rings as type; **this collection itself must fulfill a Boost.Range Random Access Range Concept**  
  
but that would require changes (e.g. use of iterator and `advance()`, `next()`, `prior()`) in every algorithms that manipulate the interior rings and I have not idea what kind of rework that imply.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-03-11 22:28:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78386628  

It's good you reminded that it's explicitly said in the concept definition.  
  
The library musn't be changed in order to support your specific (non-concept-conforming) rings container. You could implement a RandomAccess Adaptor wrapping `std::list` or any other Container with ForwardAccess. This adaptor then could be used anywhere, in particular to store interior rings of a Polygon. The effect would be the same. And as it is now, the compiler would report an error if invalid (non-RandomAccess) container was used which could harm the performance.  
  
But I'd still consider replacing `std::list` with some other container. Would it be possible to share your use case, most preferably on a list? Maybe we could think about some better workaround?

---

## Comment 7

> Username: sdebionne  
> Created_at: 2015-03-12 08:27:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/261#issuecomment-78440194  

> The library musn't be changed in order to support your specific (non-concept-conforming) rings container.   
  
Agreed.

---
