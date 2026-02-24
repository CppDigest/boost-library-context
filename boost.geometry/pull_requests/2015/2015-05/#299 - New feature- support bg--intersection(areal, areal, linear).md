# #299 New feature: support bg::intersection(areal, areal, linear) [Merged]

> Username: mkaravel  
> Created at: 2015-05-28 09:17:54 UTC  
> Updated at: 2015-06-12 05:57:47 UTC  
> Merged at: 2015-06-10 08:43:54 UTC  
> Closed at: 2015-06-10 08:43:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/299  

The purpose of this PR is to support the intersection set operation for two input areal geometries and when the output type requested is linear.  
  
The approach taken is to create boundary views of the areal geometries, and use these views as input to the linear/linear geometry intersection algorithm.  
  
The boundary view implemented currently lies in `detail` namespace, and could be moved to the `geometry` namespace if/when the need arises and when proper support for non-areal geometries is also implemented.  
  
Another intersecting aspect of the boundary view implementation is a new iterator (currently residing along with the boundary view code) that returns the rings of a polygon. If there is interest for something like this, I could move this iterator to the `iterators` directory and use it a basis to implement `ring_iterator<Areal>` iterator class for areal geometries (rings, polygons and multipolygons).  
  
On the technical side, in order to implement the boundary view for a polygon and a multipolygon I ended up creating and storing an `std::vector<>` of boundary views of the rings of the polygon and multipolygon, respectively. Notice that this is a very lightweight object as no points are copied, and only **_views**_ of the rings are stored. It was necessary to implement it this way for two reasons:  
1. I needed somehow to keep track of boundary view objects of the rings of the polygon/multipolygon.  
2. I needed to make sure that the `boundary_view<Polygon>` or `boundary_view<MultiPolygon>` object created is a model of the _MultiLinestring_ concept, and in particular that it must behave as a `Boost.Range` _Random Access Range_.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-05-28 09:43:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-106250391  

Once this PR is reviewed and merged I will update the documentation and release notes.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-05-28 18:08:09 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31261122  

Changes in this file (and probably identity_view) are not related to the problem at hand.  
  
Hope you understand, but these reviews are quite large and it is confusing to see various unrelated changes here. Or even if they are related, but necessary first steps, I would prefer to see them separately as one consistent PR with accompagnying unit tests.  
  
Anyway, my question is: what do the changes this exactly improve? I don't see at a glance in the unit tests a geometry requiring a reference_wrapper instead of a wrapper.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-05-28 18:12:16 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31261534  

Partly this is answered in the introduction (boundary view etc). I'm trying to understand how it behaves and why it does so.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-05-28 18:13:47 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31261675  

Actually they are both directly related to this PR.  
During the construction of `boundary_view<>` for polygons and multipolygons I need to push back boundary views of the rings of the polygon or multipolygon into a an `std::vector<>`. This requires copying.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-05-28 18:20:01 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31262311  

To be more precise, in order to construct the boundary view of a ring, I take its closeable view (otherwise the boundary view could be missing a segment). As the closeable view depends on the identity view or the closing view, and since I need to copy closeable views, both the closing and identity view need to be copyable.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-05-28 19:00:58 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31266431  

I understand the reasoning but this is a semantic change for Views in general. I'm not sure if this is a good idea to allow copying. Semantically this makes View concept closer to Range. Or in other words it changes something that had reference semantics into something having pointer semantics.  
  
E.g. one could think about returning them from a function and this could be dangerous in some cases.  
  
Maybe we should have some different concept for such Range-like weak Geometry pointing-"views"?  
Or maybe do you see some different, relatively simple way of implementing it?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2015-05-28 20:31:17 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31274497  

Thnks for the explanations

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-05-28 20:34:16 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31274797  

I doubt if we should do such amount of meta-programming just in unit-tests.  
Are there not more straightforward solutions?  
Why not specify Open/Close or CW/CCW as a template argument in the main test function, and call correct (as I usually do)? That is quite simple. This asks for putting it in a headerfile, share it, and you get very complex tests which in the end do the same thing.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-05-28 20:38:25 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31275188  

It's unclear to me why there are two expected WKT's which seem always the same.  
Besides that, most probably a WKT of a multi linestring having the same linestrings but in a different order are also valid output. Is that checked?  
I usually check only length (or area) - which is independent on order. Plus total number of points, for example. Admitted, different geometries could by chance produce the exactly same answer but chance is very low.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-05-28 20:43:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-106590003  

I've some comments and questions but in the end, of course, your work is greatly appreciated and we welcome this new combination of geometry types.  
  
I'm also wondering, what is the symmetry here between intersection, difference, union? A union would contain of all separate unique line-elements in boundary_views? And a difference? I would welcome some pictures (probably by mail) of the possibilities of the operations A/A -> L

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-05-29 12:42:47 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31320578  

I will take a look if I can simplify the code.

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-05-29 12:47:37 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31320881  

This has to do with the way I test equality of multilinestrings. I am not using the BG equals functionality (basically to avoid computing turns), and my equality testing procedure is fairly basic.  
I agree that I could use different measures (I have seen that in your tests). I had already implemented this as part of L/L set operations unit tests, so I was just using existing functionality.  
  
Personally what I am worried about is the `(2 1,2 1)` linestring reported in the output. It corresponds to an isolated intersection point that is detected, and in fact should not be there. It seems that it has to do with the fact that the input linestrings are closed. There was an email in our mailing list mentioning this problem, and my plan is to attack it and find a solution in a separate PR.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-05-29 12:54:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-106794736  

@barendgehrels Regarding your question about the symmetry with difference and union: the short answer is _there is none_. You cannot have this behavior for either difference or union, and it has to do with the fact that output geometries (the result of the set operation) is considered as closed. For example if you have the difference of a polygon A and a polygon B, and let's say that the intersection of A and B is part of the boundary of A, then A-B is the same as A (at least this is what our algorithm would return, and what the OGC standard expects us to return).  
  
The reasoning behind implementing intersection for A/A -> L is that there are cases where the intersection of two areal geometries is actually part of the boundary of both. It cannot be represented as a polygon (it would be invalid), so it has be output as a linear geometry (multilinestring). Another interesting case is intersection A/A -> P. Our code right now just returns all turns. In fact what `bg::intersection(A, A, P)` should return is the 0-dimensional part of the intersection which is not part of neither the 1-dimensional nor the 2-dimensional part of the intersection of the two areal geometries.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2015-05-31 15:57:48 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31391568  

@awulkiew I do understand your concerns., and I understand that you may not like to have `closing_view<>` and `identity_view<>` being copyable. I propose, then, to add a second template parameter to both these views (and inevitably a third template parameter to `closeable_view<>` as well) that would indicate if the view is supposed to be copyable. I can then implement/specialize these views to be or not be copyable, depending on the value of the template parameter. The default value would be `false` (which also means backward compatibility), and in the case of `boundary_view<>` I can simply instantiate `closeable_view<>` with `true`.  
  
I agree that the meaning is slightly different, and indeed we need something more close to our geometries (polygon, multilinestring and multipolygon are nested ranges, i.e., ranges of ranges, and I believe this is what creates the "problem").  
  
I have not found an easier way to implement this. Any suggestions are of course welcome.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-06-05 20:36:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109432744  

As far as I can tell the only pending issue was to simplify the code in the unit test. This is done in commit 3a65854.  
Are there any objections to merging this PR?

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-06-05 20:41:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109433476  

I take back what I wrote in my previous comment.  
  
There is one more issue which is how to handle `identity_view` and `closing_view`.  
The related question is whether we should keep these two views non-copyable by default or whether it is okay to make them copyable. As I mention in another comment, I can add a template parameter to them, and make them copyable according to the value of this parameter; the default behavior will then be that they are not copyable.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2015-06-05 21:13:29 UTC  
> Updated_at: 2015-06-05 23:26:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109442789  

I don't like the way how `boundary_view` is implemented. It should be possible to implement it without storing the vector of the intermediate ranges. Then the vector wouldn't be needed and we wouldn't be forced to make the views copyable which I'm not sure is a good idea. ~~Two alternative ways come to my mind~~  
- ~~implementation in terms of an iterator containing a reference to geometry and a segment/point_identifier (3 indexes) and using bg::detail::sub_range() to access specific range/Linestring.~~  
- ~~implementation in terms of concatenating and/or flattening iterator~~

---

## Comment 18

> Username: awulkiew  
> Created_at: 2015-06-05 23:25:45 UTC  
> Updated_at: 2015-06-06 08:07:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109481066  

Ah, I just realized that those must be RandomAccessRanges. So it's obvious that the ways described in the above comment are wrong.  
  
If you need to create dynamic-size storage for objects then you could allocate the memory by yourself (by dynamically creating an array of `char`s of size `sizeof(T[N])`, object of `boost::aligned_storage` or calling `std::get_temporary_buffer`) and then use placement new syntax to manually create objects using desired constructor. Copying of those views wouldn't be required since they'd be created in-place. In C++11 we could use `std::vector` for it, but unfortunately we're not allowed to do this.  
  
But I'm wondering, if a storage/container must be used for this view, is this a good candidate for a view?

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-06-06 09:32:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109557804  

The placement new idea was great.  
Commit 6aa5d27 reverts `identity_view` and `closeable_view` to their original state.  
Commit c027618 replaces `std::vector` with a dynamically allocates array that is filled using placement new.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2015-06-06 09:42:46 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31866491  

This code is not exception-safe. If the constructor of boundary_view_type thrown at some point (because e.g. the creation of a dynamic array or vector failed inside) then the already created objects wouldn't be destroyed. You should rather use std::uninitialized_copy().

---

## Comment 21

> Username: awulkiew  
> Created_at: 2015-06-06 09:43:46 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31866498  

Same here (`std::uninitialized_copy()`).

---

## Comment 22

> Username: awulkiew  
> Created_at: 2015-06-06 09:47:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109559726  

It's great that this works! However similar code was used in several places. I've implemented this dynamic array as a template which can be created using default ctors of the objects or from a range of values convertible to the elements. Would you like to use such a container?

---

## Comment 23

> Username: mkaravel  
> Created_at: 2015-06-07 14:37:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109761843  

`std::uninitialized_copy()` is now used. See commit 0bdee12.

---

## Comment 24

> Username: awulkiew  
> Created_at: 2015-06-07 15:24:40 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31877706  

A side note, this could be:  
  
```  
range_difference<  
    remove_reference<  
        interior_return_type<Polygon>::type  
    >::type  
>::type  
```

---

## Comment 25

> Username: awulkiew  
> Created_at: 2015-06-07 15:26:42 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31877735  

This is nice. Indexing from 0 allows to use unsigned type. I'm wondering why multi/ring indexes in Boost.Geometry in are signed.

---

## Comment 26

> Username: awulkiew  
> Created_at: 2015-06-07 15:29:23 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31877755  

What do you think about putting an assertion here?  
  
```  
BOOST_ASSERT(m_polygon == other.m_polygon);  
```  
  
Iterators of two different polygons shouldn't be compared.

---

## Comment 27

> Username: awulkiew  
> Created_at: 2015-06-07 15:40:08 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31877871  

Here the result of subtraction of two unsigned values is converted into signed value. Will this work on all platforms?

---

## Comment 28

> Username: awulkiew  
> Created_at: 2015-06-07 15:45:21 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31877906  

Here `range_size` of Polygon's interior_rings container could be used.

---

## Comment 29

> Username: awulkiew  
> Created_at: 2015-06-07 15:59:17 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31877999  

Great! Still this is not yet exception safe. If one of the constructors called in `uninitialized_copy` thrown an exception this exception would be propagated into the `polygon_boundary` ctor and the object would not be considered as created. The destructor would not be called in this case. Which means that the memory allocated with `get_temporary_buffer` would not be released. You could use RAII and have an automatic destroyer (similar to smart ptr), automatically deallocating the memory on destruction inside this function (if the exception was thrown this object would be destroyed). Or you could just put a try-catch block there and rethrow an exception. And since this pattern is also repeated in the implementation for MultiPolygon, you could implement a simple class for this scoped storage.

---

## Comment 30

> Username: awulkiew  
> Created_at: 2015-06-07 16:06:11 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31878041  

I just checked how exactly this function works and it seems that AFAIU it can allocate less elements than it's requested. This means that if not enough space was allocated, the constructors would be called for unallocated memory. You could e.g. replace the condition below with this:  
  
```  
result.second < m_num_rings  
```  
  
or just call `new` (and then get rid of the `bad_alloc` throw):  
  
```  
m_views = new char[sizeof(boundary_view_type) * m_num_rings];  
```  
  
Then also m_num_rings could be unsigned, e.g. `std::size_t`. It could be `range_size<[polygon interior rings range]>::type` but since it's used with `new` it probably doesn't have sense.

---

## Comment 31

> Username: mkaravel  
> Created_at: 2015-06-08 07:34:18 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31891652  

@awulkiew Done. See commit e5ff16d.  
  
BTW this change only makes sense if at the same time the type of `m_index` is changed to be the same as the `range_size` of the interior rings container (which you mention in another comment).

---

## Comment 32

> Username: mkaravel  
> Created_at: 2015-06-08 07:34:43 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31891673  

@awulkiew Done. See commit e5ff16d.

---

## Comment 33

> Username: mkaravel  
> Created_at: 2015-06-08 07:35:49 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31891729  

@awulkiew I have changed this as well. I first cast `other.m_index` and `m_index` to `Difference` before performing the subtraction. It should be fully portable now.

---

## Comment 34

> Username: mkaravel  
> Created_at: 2015-06-08 07:36:25 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31891756  

@awulkiew Done. See commit e5ff16d.

---

## Comment 35

> Username: mkaravel  
> Created_at: 2015-06-08 07:41:16 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31891985  

@awulkiew Yes, you are right. My mistake.  
I have implemented a `clear()` method that clears the allocated memory. This is called by the destructor now. I have also put the call to `std::uninitialized_copy()` inside a `try`/`catch` block; if an exception is caught, then the memory is cleared (using a call to `clear()`) and the exception is re-thrown.  
  
The code has also been re-factored and there is a single `areal_boundary` class now that handles both polygons and multipolygons. The difference between the two is:  
- how the number of rings are computed  
- how the views container is initialized  
  
I have implemented another class for performing these two tasks, specialized for polygons and multipolygons. This initialization class is now used transparently inside the `areal_boundary` class.

---

## Comment 36

> Username: mkaravel  
> Created_at: 2015-06-08 07:42:22 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31892038  

I have changed the check for `result.second` as you suggest; indeed it is better this way.  
  
I decided to keep `std::ptrdiff_t` as the type for `m_num_rings`; it seemed better to me this way.

---

## Comment 37

> Username: mkaravel  
> Created_at: 2015-06-08 07:47:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-109894459  

@awulkiew I believe I have addressed all your comments (and many thanks for them as well as the suggestions/pointers). Please let me know if it is okay for you to merge this PR now.  
  
@barendgehrels The same question: from your stand-point is it okay to merge this PR?

---

## Comment 38

> Username: awulkiew  
> Created_at: 2015-06-08 09:11:41 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31897232  

I'm glad that you extracted this to be a separate function, because now it's more clear that this function it is not safe. If one of the calls to `std::uninitialized_copy` fails then the elements before `cur_it` are constructed and the elements after `cur_it` are left not constructed. And the former elements aren't destroyed here (and they should be). You're trying to handle the case in `clear()` but AFAIU this function tries to call the destructors of ALL of the objects. In this case only a part of them is created so the destructors are called for not created objects.

---

## Comment 39

> Username: awulkiew  
> Created_at: 2015-06-08 09:13:16 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31897322  

My suggestion would be to either get rid of the loop by passing an iterator to a flattened range (if that's possible), or check if the exception was thrown manually with try-catch.

---

## Comment 40

> Username: awulkiew  
> Created_at: 2015-06-08 09:14:34 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31897398  

As mentioned in another comment, An `initializer::apply()` should be made exception-safe. So `clear()` shouldn't be called here.

---

## Comment 41

> Username: awulkiew  
> Created_at: 2015-06-08 09:24:57 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31898095  

At least in STL `clear` name is used for a Container's function removing elements from it. In the case of `vector` it destroys the elements but keeps the allocated memory but in the case of a list it deallocate the memory as well. So AFAIU it's a higher-level abstraction than this "deinitialization" routine. Using `clear` name may be confusing here, at least for me is counter-intuitive. The name should probably also somehow refer to the naming of initialization counterpart. So it could be e.g. `deinitializer::apply()` or `deinitialize()`.

---

## Comment 42

> Username: awulkiew  
> Created_at: 2015-06-08 09:30:04 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31898446  

When m_num_rings was compared with `0` this code worked ok since the memory wasn't allocated so we wouldn't be forced to remove anything. However now, assuming that a space for more than `0` but less than `m_num_rings` may be allocated here after throwing `bad_alloc` the allocated memory is not deallocated in this case.  
  
Btw, `new` would either allocate all of them or throw an exception so exactly what's needed.

---

## Comment 43

> Username: awulkiew  
> Created_at: 2015-06-08 09:32:51 UTC  
> Updated_at: 2015-06-08 22:27:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31898621  

Sorry, I must clarify. A loop destroying the elements shouldn't be called. However the memory still would have to be deallocated using either try-catch and `return_temporary_buffer`/`delete` call or some automatic-smart-ptr-like-deallocator.

---

## Comment 44

> Username: awulkiew  
> Created_at: 2015-06-08 22:47:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31967965  

This is interesting. It implies that the empty range is [NULL, NULL+0). It should be fine I think.

---

## Comment 45

> Username: awulkiew  
> Created_at: 2015-06-08 22:48:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#discussion_r31967996  

Yes, using RAII here makes the code more clear. Thanks!

---

## Comment 46

> Username: awulkiew  
> Created_at: 2015-06-08 22:48:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110165390  

Thanks! I'm ok with merging.

---

## Comment 47

> Username: mkaravel  
> Created_at: 2015-06-08 22:49:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110165551  

@awulkiew Thanks a lot for your comments. They have made the code much much better.

---

## Comment 48

> Username: mkaravel  
> Created_at: 2015-06-08 22:50:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110165704  

@barendgehrels Are you okay with merging this PR?

---

## Comment 49

> Username: barendgehrels  
> Created_at: 2015-06-09 06:34:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110245151  

I could not yet follow all conversations about this PR. So not yet, I'm specificly interested why dynamic memory is used at all - we normally don' t do that directly ( though indirectly of course, by using std).

---

## Comment 50

> Username: mkaravel  
> Created_at: 2015-06-09 07:11:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110255581  

The original problem was to create a view so that via this view we can see the rings of a polygon/multipolygon as linestrings in a multilinestring.  
  
My original approach for this was to keep track of a `std::vector<>` of views of these rings as linestrings. Using a `std::vector<>` however has the _drawback_ that the elements inserted in the vector must be copyable. This was the reason why I originally made both `identity_view` and `closing_view` copyable.  
  
Adam did not like the idea of having copyable views (and I understand his point of view and share it now). So, instead of using a `std::vector`, he proposed to dynamically allocate memory (raw _uninitialized_ memory to be exact) and _place_ the views of the rings in this dynamically allocated memory. The keywords here are **_uninitialized**_ and **_place**_: using `placement new` syntax or `std::uninitialized_copy()` it is possible to create objects of some type and place them directly to some already reserved/allocated memory without calling the copy constructor or the assignment operator.  
  
This was the reason why dynamic memory allocation was suggested: I needed a container whose elements are not initialized (to use with `std::uninitialize_copy()`), and at the same time its size was only known at run time (hence the need for _dynamic_ allocation).  
  
The rest of the discussion was about how to make the code exception-safe and exception-neutral.

---

## Comment 51

> Username: barendgehrels  
> Created_at: 2015-06-09 20:34:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110496305  

Thanks for the explanation, it is a clear summary.  
  
I agree to keep the two views non-copyable.  
  
Is this the first time we use these techniques? I think so, but maybe they are earlier used in Index?  
  
Are there alternatives? I think so, but probably this needs another kind of combinations of iterators/ranges.  
  
Anyway, I don't object to merging this PR (thanks - it is a considerable amount of work and new functionality). But I'm still curious if this problem cannot be solved by the approaches we already used, instead of selecting a new approach.

---

## Comment 52

> Username: mkaravel  
> Created_at: 2015-06-10 08:43:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110657051  

@barendgehrels It is the first time for me. Adam may have used something similar in Index.  
  
An alternative would always be to construct the linear geometry corresponding to the boundary of the areal geometry. But this is something I wanted to avoid from the very beginning (i.e., to construct a new geometry).  
  
For the past two weeks that this PR has been open, I could not figure out another way to create the boundary view. Qualitatively, what is different here compared to all other views we have had so far, is that all other views operate on ranges whose elements are points, whereas this is the first case where we want to have a view on a range whose elements are ranges of points (so here we have nested ranges). The nested ranges have to conform to the same view as well, and this is IMHO where this need for an alternative design came up.

---

## Comment 53

> Username: awulkiew  
> Created_at: 2015-06-10 10:19:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110683397  

There are several problems related to this PR which we was forced to address:  
1. The flattened BidirectionalRange of Rings in MultiMolygon must be somehow mapped into the RandomAccessRange of Linestrings of MultiLinestring. This means that to allow access in O(1) we need to build a RandomAccess Container of mapping objects. They can be views/pointers/iterators (as in this PR) or some indexes/IDs (see 2), etc. The fact that we must create a container may be an indication that this is not a good candidate for a view (so it could be named somehow else), nevertheless I don't see another way of implementing such utility if the O(1) access is a requirement.  
2. Currently the library cannot handle the multi-geometries returning a NON-true reference to a single geometry (MultiGeometry element). If it was possible, then we could store `ring_index`es in a container and then on dereference of `boundary_view::iterator` return a new object, let's say `linestring_range_view` by value. The support of course depends on the algorithm, but at least the `sectionalize`/`partition`/`get_turns` blows up the program because of a segfault caused by returning a reference to locally created object AFAIR. This probably should be fixed/supported.  
3. It isn't possible to store non-copyable objects in C++03 `std::vector`. So if we want to preserve the non-copyable requirement for views we must create a container ourselves. We could or even should implement a reusable container allowing to do the things that was did in this PR. Though I don't have an idea how the interface should look like to be intuitive and safe. Definietly something we could think about.  
  
And yes, similar techniques are widely used in the Index.

---

## Comment 54

> Username: mkaravel  
> Created_at: 2015-06-10 11:54:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/299#issuecomment-110716678  

Adam, that was very comprehensive! Thanks!  
  
I would argue that item 2 is the biggest problem, and this was the original problem I stumbled upon when trying to return boundary views to rings by value (actual objects, not references to boundary views) from within the boundary view of a polygon/multipolygon. It was this problem that originally prompted me to go for a container (`std::vector<>`) or views, but as Adam explains, this required that views are copyable (at least for C++03).  
  
I like the current solution a lot. I agree that another name (not `_view`) might be better.  
  
We should definitely brainstorm about whether we want to support multigeometries whose iterators return by value, and how to do that.

---
