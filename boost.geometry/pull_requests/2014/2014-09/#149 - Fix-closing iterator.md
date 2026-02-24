# #149 Fix/closing iterator [Closed]

> Username: awulkiew  
> Created at: 2014-09-30 22:54:18 UTC  
> Updated at: 2014-10-08 13:49:54 UTC  
> Closed at: 2014-10-01 15:03:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/149  

This PR adds a support for mutable Geometries to `closing_iterator<>`.  
  
The rationale for this change:  
1. In the comments of `closing_iterator<>` it states:  
_Use with "closing_iterator<Range> or "closing_iterator<Range const>to get non-const / const behaviour_  
2. Without it `closeable_view<>` can't handle mutable Geometries so, e.g. can't be used in the implementation of `for_each_segment()` (see: https://github.com/boostorg/geometry/pull/147).  
  
This PR replaces the value_type used by `closing_iterator<>` from `range_value<R>::type const` to the one defined by `add_const_if_c<is_const<R>::value, range_value<R>::type>::type`.  
  
Though I'm not sure if this fix should be applied. Supporting mutable geometries in `closing_iterator<>` means supporting them in `closeable_view<>` and I'm not sure if this is intended since this view is used internally only with non-mutable Geometries (not taking into account the change made by https://github.com/boostorg/geometry/pull/147).  
  
We could of course "fix" the iterator but use `closing_iterator<Geometry const>` for both `iterator` and `const_iterator` types in `closeable_view<>` but then this view couldn't be used in `for_each_segment()` and honestly the "fix" wouldn't have much sense since this iterator is only used by the view.  
  
So the question is, should views handle only non-mutable Geometries or could this requirement be relaxed, at least for some of them?

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-10-01 06:03:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57422621  

I totally agree with the changes in closing iterator for the following reasons:  
- The current implementation of the closing iterator (before this PR) is not in sync with the documentation.  
- The previous implementation of the closing iterator prevented its usage in the segment iterator for mutable geometries (see also comments in PR #141). Currently the segment iterator is provided only for non-mutable geometries, but if we decide to change this, we would have to make the changes proposed by Adam in this PR.  
  
As a side note, as of PR #141, the closing iterator is not only used by the closeable view but also by the segment iterator.  
  
At this point I would prefer that `closeable_view` works with non-mutable geometries only. We can change this later on, after the discussions about how we should access mutable open/closed geometries have matured a bit more.  
  
Regarding `for_each_segment` it is very easy to come up with an alternate fix that does not use the closeable view. I will propose a patch in this direction, and see discuss if we prefer that alternative.  
  
@awulkiew BTW, do we use `for_each_segment` with mutable geometries somewhere in BG? Do we have a unit test for that?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-10-01 06:34:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57424448  

See PR #150 for the alternate bug fix that does not use the closeable view.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-10-01 08:30:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#discussion_r18266417  

I thought our usage or add_const_if_c was deprecated, but maybe it is absolutely necessary here. Can you confirm that?  
Can you add boost::is_const (consistent iwth boost::range_value)

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-10-01 08:30:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#discussion_r18266429  

opening bracket should go next line

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2014-10-01 08:37:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57434408  

The conclusion is not yet clear. Do we want to support mutable (closing_iterator, closeable_view, segment_iterator)? What are the consequences? If the type is non const, we probably should support them in the future. Suppose you want to implement a transformation on an open ring. So all points are shifted. That might be a valid scenario, however if you just use the closing_iterator the closing point (which is the starting point an open ring) will be transformed twice. So that is an argument to _not_ allow it. Are there scenarios needing it?

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-01 10:22:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57444725  

As I mentioned in my previous post, I suggest we keep the `closeable_view` and `segment_iterator` as non-mutable for now.  
For the closing iterator, we either need to fix the doc, or support mutable geometries.  
  
Given that 1.57 is approaching I would suggest to keep all three as non-mutable (as they are now), fix the doc for the `closing_iterator` and use the alternative bug fix for `for_each_segment` in PR #150.  
  
@barendgehrels Regarding the scenario you mention: you can use the (mutable) point iterator to do this. The point iterator accesses only the stored points, irrespectively of whether the geometry is closed or open. So we basically have the functionality for doing what you suggest (assuming you are just interested in shifting just the points of the geometry and nothing else).

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-10-01 10:24:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57444905  

@mkaravel No we don't use `closing_iterator`/`closeable_view` with mutable Geometries. I was testing convex_hull and for test purposes used `for_each_segment()` which after merging https://github.com/boostorg/geometry/pull/147 stopped compiling for the code similar to this one where mutable `polygon` is passed:  
  
```  
polygon poly;  
bg::for_each_segment(poly, info_printer());  
```  
  
@barendgehrels Is your remark about deprecation of `add_const_if_c` about that views shouldn't work for non-mutable Geometries or that `add_const_if_c` shouldn't be used at all (it is in various places)? Or that checking `is_const<>` for a Range won't reflect the constness of values in a general case, e.g. when a Range is a temporary object returned by some RangeAdaptor?  
  
@barendgehrels Since there is already an alternative fix for `for_each_segment` we can drop this PR. However this means that if non-mutable Ranges are required by `closing_iterator` then the doxygen info for `closing_iterator<>` is wrong. Regarding your remark about the transformation, the same is true for `for_each_segment()`, `for_each_point()` and `point_iterator<>`.  
  
So I see those possible solutions:  
1. just close this PR.  
2. fix the `closing_iterator<>` info by explicitly saying that non-mutable Ranges are required.  
3. support mutable Ranges in `closing_iterator<>` but not in `closeable_view<>`  
4. apply this PR which means adding a support for mutable Ranges also in `closeable_view<>`  
I'm ok with 1 or 2.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-10-01 10:25:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#discussion_r18270560  

It is necessary. I have had similar issues in point and segment iterator, where I had to explicitly qualify the range value with const. I am not using `add_const_if_c`, but some other metafunction I wrote. But I guess I should probably remove the custom metafunctions and use `add_const_if_c`.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-10-01 10:28:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57445250  

I support both 1 and 2 (I am assuming here that 2 will be the treated by another PR).

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-10-01 10:39:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57446152  

@mkaravel Regarding the `point_iterator<>`. To be consistent with the rest of the iterators shouldn't it also work only for non-mutable Geometries?  
  
@mkaravel In order to merge the solution 2 I'd close this PR and probably directly push the change into develop since it's trivial.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2014-10-01 10:46:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#discussion_r18271327  

I'm wondering why it was deprecated. It's possible that this approach may not work for all Ranges. I didn't test it yet but e.g. when a tempoarary range was generated by operator| it would be a const object but still handling mutable values. So maybe we should come up with some better solution/tool for Ranges.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2014-10-01 13:02:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57459361  

OK about making them non-mutable

---

## Comment 13

> Username: mkaravel  
> Created_at: 2014-10-01 13:28:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57462561  

@barendgehrels I am bit confused: "them" refers to what?

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-10-01 13:29:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57462722  

@awulkiew   
- The point iterator does not suffer AFAIU from the problems of the other iterators. If Barend and you prefer that it is non-mutable I can easily fix that.  
- Sure, do it however you prefer.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2014-10-01 15:03:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/149#issuecomment-57478055  

@mkaravel I'm guessing that Barend had the iterators in mind. I have no preference regarding the `point_iterator<>`, I just pointed out that making it const would be consistent with the rest of the iterators and views. Maybe even slightly safer since it wouldn't be possible to make mistakes like the modification of the first Point 2x if someone decided to use `point_iterator<>` for this. Anyway, I'm closing this PR.

---
