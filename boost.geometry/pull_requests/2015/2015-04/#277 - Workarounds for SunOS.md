# #277 Workarounds for SunOS [Merged]

> Username: awulkiew  
> Created at: 2015-04-01 22:38:18 UTC  
> Updated at: 2015-04-09 01:13:58 UTC  
> Merged at: 2015-04-08 15:23:53 UTC  
> Closed at: 2015-04-08 15:23:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/277  

The proposed changes are workarounds, the code compiles on other platforms. Though I think merging them could be beneficient. Plus, there would be more green in the matrix.  
  
There are a few more failing tests for which workarounds (2 issues) could be added:  
- Spherical [distance](http://www.boost.org/development/tests/develop/developer/output/oracle-intel-S2-stlport4-boost-bin-v2-libs-geometry-test-algorithms-distance-distance_se_pl_pl-test-sun-stlport4-release-address-model-32-architecture-x86-threading-multi.html) tests - the use of `comparable_type` private member in the definition of public member struct template `haversine<>::calculation_type<>`, it's hard to believe that this fails only in haversine.  
- [point_iterator](http://www.boost.org/development/tests/develop/developer/output/oracle-intel-S2-stlport4-boost-bin-v2-libs-geometry-test-iterators-point_iterator-test-sun-stlport4-release-address-model-32-architecture-x86-threading-multi.html) test - the implicit conversion of reverse iterator to const reverse iterator in the test itself. E.g. only assignment/explicit conversion and comparison could be tested, without implicit conversion.  
  
The workarounds would be trivial, what do you think?

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-04-02 17:30:24 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27678435  

What was wrong with this code? This is standard vector code - I cannot imagine that either layout changes or std code is causing issues.  
  
Anyway, this code will go so I don't object to the change.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-04-02 18:26:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#issuecomment-89000667  

@awulkiew I would like to take a closer look at it. Will probably do this Monday, if not earlier.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-04-02 18:27:38 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27683679  

It fails in the `rit != pc.robust_turns.rend()` line. AFAIU it's because the compiler doesn't implicitly convert the mutable reverse iterator to const reverse iterator before comparison. `rbegin()` and `rend()` return mutable reverse iterators when the container is mutable, which is this case. Those mutable iterators must then be converted into const iterators in the original code. So in the above case the implicit conversions are ommited, const reverse iterators are created explicitly, which should help.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-04-05 09:58:36 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27776124  

Thanks for explaining - I'm OK with merging

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-04-06 07:52:08 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27788252  

Following the explanation you gave to Barend for the issue in `buffered_piece_collection.hpp`, my suggestion would be to re-write lines 222 and 223 in the file as:  
  
```  
seg_or_box_iterator_type it_min1 = boost::const_begin(seg_or_box_points);  
seg_or_box_iterator_type it_min2 = ++boost::const_begin(seg_or_box_points);  
```  
  
Please remember to write these in two lines, to be conformant with the 80-chars-per-line requirement.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-04-06 07:56:17 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27788345  

Similarly here, I would write like 50 and 51 as:  
  
```  
iterator it = boost::const_begin(view);  
iterator next = ++boost::const_begin(view);  
```  
  
Also in the for-loop at line 52 I would use `next != boost::const_end(view)`.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-04-06 07:57:38 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27788377  

For this one I think we should leave it as it is and modify how this is called. The `AccessInnerEnd` class should return a const iterator instead of a mutable iterator, and this should take care of it.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-04-06 07:58:19 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27788406  

I will check where this is called from and suggest a change.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-04-06 08:35:04 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27789223  

I propose to re-write `decrement()` as follows:  
  
```  
    inline void decrement()  
    {  
        if ( m_outer_it == m_outer_end  
             || m_inner_it == AccessInnerBegin::apply(*m_outer_it) )  
        {  
            do  
            {  
                --m_outer_it;  
            }  
            while ( empty(m_outer_it) );  
            m_inner_it = AccessInnerEnd::apply(*m_outer_it);  
        }  
        --m_inner_it;  
    }  
```

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-04-06 12:33:21 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27794800  

Ok, I'll take a look at it later. Using const iterators explicitly shoudl of course be safer, though this is a different change. Have in mind that the conversion from mutable to const iterator is not a problem here and below. This is a different issue than the one I explained.  
  
Here the compiler complains about calling pre-increment or post-increment operator for temporary objects. In general this works when an operator is a member function of a class. But wouldn't work e.g. if the iterator was a pointer, or if the user-defined operator was a free function because rvalue couldn't be converted to non-const reference. And AFAIU on Solaris Compiler it doesn't work in any case.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-04-07 11:53:06 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27870778  

So I think that your proposal should still fail on Solaris. It could be:  
  
```  
seg_or_box_iterator_type it_min1 = boost::const_begin(seg_or_box_points);  
seg_or_box_iterator_type it_min2 = it_min2;  
++it_min2;  
```  
  
or  
  
```  
seg_or_box_iterator_type it_min1 = boost::const_begin(seg_or_box_points);  
seg_or_box_iterator_type it_min2 = boost::prior(boost::const_begin(seg_or_box_points));  
```  
  
EDIT: of course assuming that seg_or_box_iterator_type is const iterator type.  
  
I prefer the first one because it's one dependency less, less functions to debug, probably faster for some iterators which musn't be created two times, boost::prior() call doesn't look intuitive enough for me etc. But I can be persuaded otherwise :)

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-04-07 11:58:32 UTC  
> Updated_at: 2015-04-08 12:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27871085  

And then the line 52 would fail from the reason I described before. For this to work also `iterator next` type should be changed to `const_iterator`.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-04-08 12:23:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/277#discussion_r27964190  

Actually the above may not be true. It's possible that only `std::vector<>::const_reverse_iterator` fails on Solaris this way. Either way, the code should compile now.

---
