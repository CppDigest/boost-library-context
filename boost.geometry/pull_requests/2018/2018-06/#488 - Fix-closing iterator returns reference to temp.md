# #488 Fix/closing iterator returns reference to temp [Merged]

> Username: jonasdmentia  
> Created at: 2018-06-07 00:11:32 UTC  
> Updated at: 2018-07-09 11:44:50 UTC  
> Merged at: 2018-07-09 11:43:46 UTC  
> Closed at: 2018-07-09 11:43:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/488  

This PR addresses an issue with both closing_iterator and ever_circling_range_iterator where they can return a reference to a temporary object. This happens when using a range where Range::reference is a value type (in my particular case, I hit this with boost::transformed_range). This happens because neither of them were honoring the range's reference type, they were assuming it was 'value_type const&'.  
  
The first commit adds the tests, both of which fail with a SEGFAULT, and the second commit fixes the iterators.  
  
Built and tested on Ubuntu 16.04 with gcc 5.4:  
gcc (Ubuntu 5.4.0-6ubuntu1~16.04.9) 5.4.0 20160609

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-06-07 11:56:49 UTC  
> Updated_at: 2018-06-07 12:01:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#issuecomment-395394306  

Thanks!  
  
Note that transformed range is SinglePassRange, not RandomAccesRange which is required by the library (see Linestring and Ring concepts). It's because as you noticed non-real-reference has to be returned and the standard allows only InputIterator to do that. AFAIR `iterator_facade` will model InputIterator even though `boost::random_access_traversal_tag` was passed. So in general if you wanted to be safe you should avoid passing non-RandomAccessRanges because someone, somewhere in the code might assume that passed ranges and iterators should work in a certain way, i.e. always return real reference. For instance, instead of `range_reference<const Range>::type` someone might have used `range_value<Range>::type const&`. Reference might have been wrapped inside a class and stored in a container which would result in dangling reference, e.g. consider this:  
  
    template <typename T>  
    struct ref_wrapper  
    {  
        wrapper(T const& r) : ref(r) {}  
        T const& ref;  
    };  
  
    // ...  
  
    // Range const& range  
    ref_wrapper<boost::range_value<const Range>::type> ref(*boost::begin(range));  
    // at this point wrapped reference is dangling reference if Range's iterator  
    // returns temporary object convertible to value_type  
  
With that said I think the code should work in practice so I'm ok with the change. But if you want to be more complete you could go through the code and see if the iterators you just modified are used as intended. So please let me know if you'd like to have this PR merged at this point or work on it more.

---

## Review 2 [Commented]

> Username: mkaravel  
> Created_at: 2018-06-07 16:59:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/488#pullrequestreview-126871629  

📁 test/iterators/ever_circling_iterator.cpp

```diff
  78 |         std::ostringstream out;
  77 |-         bg::ever_circling_range_iterator<G> it(geo);
  79 |+         bg::ever_circling_range_iterator<G const> it(geo);
```

> Username: mkaravel  
> Created_at: 2018-06-07 16:59:37 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r193820916  

Why did you need to modify this line? I would expect the ever_circling range iterator to work with or with out the ``const``.

> Username: jonasdmentia  
> Created_at: 2018-06-07 22:07:23 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r193903205  

ever_circling_range_iterator stores a non-const pointer to Range, but if the range you're constructing the iterator with is const then you end up trying to assign a pointer to a const range to the non-const pointer.  I think ever_circling_range_iterator is doing the right thing here, it assumes the const-ness is captured as part of the Range template parameter, it's just that at the call site the G template parameter _doesn't_ capture the const-ness and the variable geo is a 'G const&'....hence the explicit const.  
  
This didn't matter before because geo was a local non-const variable, but now that I've separated the WKT parsing and pass geo in as 'G const&' it does. Note that the same thing was already being done in the iterator typedef used with ever_circling_iterator (i.e. the non-range version), I suspect it wasn't actually needed before for the same reason (geo was non-const) but it is now.


---

## Review 3 [Commented]

> Username: mkaravel  
> Created_at: 2018-06-07 17:00:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/488#pullrequestreview-126872051  

📁 include/boost/geometry/iterators/closing_iterator.hpp

```diff
  44 |     >
  45 | {
  46 |+     typedef typename boost::range_reference<Range>::type const reference_type;
```

> Username: mkaravel  
> Created_at: 2018-06-07 17:00:50 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r193821236  

I think the proper way to define those is to use the types from the `iterator_facade` class.  
Same for `difference_type`.

> Username: mkaravel  
> Created_at: 2018-06-07 17:03:49 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r193822119  

It really does not matter: I just checked the ``iterator_facade`` class and the types you provide are those used to define ``iterator_facade::reference`` and ``iterator_facade::difference_type``. So you code is perfectly fine.  
  
My comment should be understood as a stylistic one.

> Username: jonasdmentia  
> Created_at: 2018-06-07 22:19:13 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r193905593  

Yep, totally agree. Fixed.


---

## Review 4 [Commented]

> Username: mkaravel  
> Created_at: 2018-06-07 17:01:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/488#pullrequestreview-126872110  

📁 include/boost/geometry/iterators/ever_circling_iterator.hpp

```diff
 122 | 
 121 |-     typedef std::ptrdiff_t difference_type;
 123 |+     typedef typename boost::range_reference<Range>::type const reference_type;
```

> Username: mkaravel  
> Created_at: 2018-06-07 17:01:01 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r193821278  

Same comment here.


---

## Comment 5

> Username: jonasdmentia  
> Created_at: 2018-06-07 23:56:42 UTC  
> Updated_at: 2018-06-08 00:03:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#issuecomment-395602135  

Thanks @awulkiew , I'm aware that in general you need a RandomAccessRange, but in my particular case I was using closeable_view directly and ran into this, along the lines of:  
```  
std::vector<Position> positions;  
bg::models::ring<TransformedPointType> ring;  
bg::assign(ring, closeable_view(positions | transformed(some_transform_func)));  
```  
Easy enough to work around, but there didn't seem to be any good reason it shouldn't work :)  
  
I did scan through to look for anything obvious that was using closing_iterator (either directly or indirectly) and most of it looked OK, but there were some places that were passing a range wrapped with closeable_view (or normalized_view) that could be a problem depending on what the callee does with it. the only place I could find that's definitely a problem was in in algorithms/detail/relate/linear_areal.hpp in the calculate_from_inside() method, it stores a reference to the result of range::at(rng, index) where rng is a normalized_view (that derives from closeable_view).   
  
So, I think merge the PR as is. I think it's fair to say the majority of the library requires a RandomAccessRange with occasional bits (like closeable_view) that can work SinglePassRange.

---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-04 13:24:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/488#pullrequestreview-134378820  

📁 include/boost/geometry/iterators/closing_iterator.hpp

```diff
  41 |-         boost::random_access_traversal_tag
  41 |+         boost::random_access_traversal_tag,
  42 |+         typename boost::range_reference<Range>::type const,
```

> Username: awulkiew  
> Created_at: 2018-07-04 13:24:11 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r200125473  

Typically one should use `boost::range_reference<Range const>::type` to get reference for const range. Why not in this case?

> Username: jonasdmentia  
> Created_at: 2018-07-05 11:40:58 UTC  
> Updated_at: 2018-07-05 13:08:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r200321361  

I just carried forward the same style used with boost::range_value above it on line 40, but thinking about it there could be a difference if the range is using a proxy type instead of a regular reference (which I don't think would be an issue with range's value type?). I'll fix it up.

> Username: awulkiew  
> Created_at: 2018-07-09 11:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#discussion_r200971966  

I think for value type it should be ok but we'll see. If anything goes wrong the tests on various platforms should detect that.


---

## Comment 7

> Username: awulkiew  
> Created_at: 2018-07-09 11:43:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/488#issuecomment-403451147  

Thanks!

---
