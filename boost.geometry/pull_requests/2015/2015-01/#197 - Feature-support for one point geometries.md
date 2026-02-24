# #197 Feature/support for one point geometries [Merged]

> Username: mkaravel  
> Created at: 2015-01-22 16:37:17 UTC  
> Updated at: 2015-02-03 20:01:00 UTC  
> Merged at: 2015-01-28 22:24:42 UTC  
> Closed at: 2015-01-28 22:24:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/197  

In this PR I modify the `range_segment_iterator` class to support ranges with less than two elements.  
With this modification of the `range_segment_iterator` class, the `segment_iterator` can support (invalid) geometries that contain ranges with less than two points.  
  
A consequence of this PR is that `bg::distance` can now compute the distance for (invalid) geometries that contain ranges of less than two points (e.g., one-point linestrings, non-empty closed rings with less than four points, non-empty open rings with less than three points, non-empty multilinestrings that contain one-point linestrings (or even empty linestrings), one-point polygons, etc.)  
  
The unit tests in this PR rely on the fix proposed in PR #196, so PR #196 should be merged first.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-01-23 22:18:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71276212  

This version of segment_iterator is maybe less "clear" but in the same time in some cases (se below) it seems to be more robust. For a 1-point range a degenerated segment can be dereferenced instead an assertion failure. And if someone wanted to handle the degenerated ranges in other way he can still do it. Besides AFAIU it's always possible to refer to a degenerated segment anyway. Or are such segments normally filtered by the algorithm?  
  
I wrote "in some cases" because there is a special case when this implementation could silently fail, i.e. if mutable segments was represented. Then suppose someone would like to modify the points through the refering segment. In such scenario the same iterator would be dereferenced for both points. So when someone modified the first point, the second one would also be modifed. This could be unexpected. I can't see why someone could want to modify the points through the segment iterator, still maybe it could be safer to dissallow it, so always return non-mutable Reference and write a comment about it. Or was this done already in other part of the code? I remember we had similar conversation some time ago.  
  
Btw, what if a MultiLinestring contained an empty Linestring? Is such Linestring just ignored? By concatenating/flattening iterator or somehow else? Can default-created/singular/NULL iterators be compared or is the emptiness of a range checked explicitly somehow (`boost::empty()` or `boost::size()`)? I'm guessing that the latter is true, but I'm asking just in case. In C++03 the use of singular iterators is undefined behavior.  
  
Regarding distance, personally I'd probably consider implementing the distance "the usual way" so "manually" wrapping an algorithm for single-geometry as a policy of an algorithm for multi-geometry in dispatch namespace.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-01-24 20:58:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71336936  

@awulkiew Please see below. I have tried to provide answers to your questions:  
- Yes, you can always dereference degenerated segments through the `range_segment_iterator` or the `segment_iterator`. Whether the algorithm using the segment iterator filters degenerated segments is the responsibility of this algorithm.  
- Correct: if the segment iterator it mutable, then you can have the problem you mentioned. The current implementation of the segment iterator is non-mutable, so this problem does not exist.  
- Multilinestrings with empty linestrings are handled: the empty linestring is ignored. This is done through the flatten iterator automatically. See the unit test for the `segment_iterator` iterator class.  
- The segment iterator is a bidirectional iterator, and its traversal tag is also the bidirectional traversal tag. Its unit test checks for the corresponding concepts from Boost.Concept and Boost.Range. Its emptiness is checked the usual way: `begin == end`. I have not checked `boost::empty()`, but I do expect that it works. Using `boost::size()` should be possible but a bad idea: it would take linear time to compute the size. See also the unit test for the segment iterator where all sorts of (invalid) geometries are tested. I do not test singular iterators at all.  
- Regarding distance: I do not see the point of doing it the manual way. Suppose we have two multilinestrings. The current algorithm takes the segments of the smallest multilinestring (smallest in terms of number of segments), inserts its segments in an r-tree and then the r-tree is queried for nearest neighbors using the segments of the other multilinestring. The minimum distance among nearest neighbor pairs is returned as the end. Let's consider the alternative, manual way. First of all I would avoid reducing the problem to finding the minimum distance among all pairs of linestrings in the multilinestrings, as this would result in a quadratic algorithm. So what I would possibly do is to consider each multipolygon, manually convert 1-point linestrings to segments, and insert all segments inside an r-tree. But this is basically what is done now using the patched `range_segment_iterator` proposed in this PR. If I understand it correctly, your suggestion to wrap an algorithm for a single geometry as a policy for the multigeometry would easily result in quadratic behavior (think of two multilinestrings where each linestring consists of a single point or a single segment).

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-01-28 10:53:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71815067  

Would it be possible to make a decision about this PR?  
Ideally I would like it to be in 1.58.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-01-28 11:19:20 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23680281  

is std::distance not working?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-01-28 11:20:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71818256  

See my question.  
For the rest, Adam did have a close to this. If Adam is OK, I'm OK with merging

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-01-28 12:29:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71827502  

Yes, I'm ok with merging. I'll wait for a response for the question about the std::distance().

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-01-28 13:52:43 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23687549  

If the range is a random access range, calling `std::distance` is perfectly okay.  
If the range is not a random access range, calling `std::distance` takes linear time in the size of the range.  
  
It is true that in our context, the range passed here is either a ring or a linestring, and our corresponding concepts require that they are of random access. So it is okay to use the following return statement:  
  
```  
return std::distance(first, last) < 3u;  
```  
  
Let me know if this preferable, and I will make the change.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-01-28 13:53:28 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23687593  

The return statement in my previous message should have been:  
  
```  
return std::distance(first, last) < 2u;  
```

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2015-01-28 14:07:27 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23688396  

If that is the case. Then I think boost::size(r) < 2u will also do at the caller side?  
We don't need this function then at all. That looks better to me.  
All (or at least most of) our functionality is based on random access ranges, accessing them with segment-id's and so on, I don't think we have to provide complexity to support non random access ranges here.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-01-28 14:09:28 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23688517  

We normally put a space after !

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-01-28 14:10:20 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23688564  

OK I've never seen this usage for bool but I assume it initializes with false

---

## Comment 12

> Username: mkaravel  
> Created_at: 2015-01-28 14:15:31 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23688827  

All right. I will use `boost::size` then. This way I can avoid the additional `#include`.

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-01-28 14:15:48 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23688840  

Will do. Thanks for pointing this out.

---

## Comment 14

> Username: mkaravel  
> Created_at: 2015-01-28 14:17:13 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23688913  

Using the default constructed iterator is undefined behavior, which is why I think it is okay to leave it as it is.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2015-01-28 14:25:42 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23689515  

OK, thanks. I'm OK with merging after the update

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-01-28 15:40:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71855412  

This PR should be ready for merging now.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2015-01-28 15:49:56 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23696059  

Why not explicitly initialize it with false?  
Consider the initialization of a pointer with NULL.  
I see it in various places in BG code, so it's a more general problem...  
  
Ah ok, I see now that your argument was that this default-ctor doesn't initialize the members. On the other hand you're value-initializing the members. At least with C++11 iterators this should work. If the goal was to not initialize anything then why put this bool member in the initialization list?  
  
IMO the values should just be initialized, the iterator value-initialized as it is now, and the bool flag explicitly.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2015-01-28 16:18:41 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23698520  

I agree with initializing it with false.  
  
I don't agree with the statement "I see it in various places, it is a general problem": those are assignments of setting a numeric type T to 0 (T()), which is perfectly fine and allowed. (as long as udt do support also this construct)

---

## Comment 19

> Username: awulkiew  
> Created_at: 2015-01-28 16:54:58 UTC  
> Updated_at: 2015-01-28 17:19:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23701727  

As long as we're in C++11 or a constructor of T in C++98 initializes T to 0. Otherwise, this is not true. This is why someone was forced to add a default ctor to ttmath types in the past.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-01-28 17:20:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23703967  

Initialization with `false` done.

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2015-01-28 21:40:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23726230  

T x = T() for a built-in type is well defined zero-initialization in C++03  
ttmath is not a built-in type so I was the one adding that ctor to support this construct  
Here it was used differently so good Menelaos changed it

---

## Comment 22

> Username: awulkiew  
> Created_at: 2015-01-28 22:19:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23729199  

@mkaravel Thanks!  
  
@barendgehrels Indeed scalar types should be zero-initialized assuming the compiler is standard-conformant. However Geometry should work also with non-scalar coordinate types and we shouldn't assume that a default ctor should initialize an object to an equivalent of 0. Is there a reason why do you think that value-initialization is better than explicit initialization with 0? This is one more, unnecessary requirement for coordinate type. The library already requires that it should be possible to construct an object with some int or double value, e.g. in distance_cross_track:  
  
```  
return_type const quarter(0.25);  
```  
  
which also has its drawbacks but it's another story. Why not do this also for 0 and leave the default ctor out of this?

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2015-01-28 22:40:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23730689  

@awulkiew That is a different thing.You mentioned it is a general problem in BG. I reacted on that: it is not a general problem in BG.  
  
UDT are a different thing - if you cannot assume they have a default constructor, you can also not assume the presence of a constructor T(0). Therefore we selected 7 years ago the construction of T(): it is well defined.  
  
Eehm, as far as I know I never wrote the construct as you mention for quarter - this should IMO _not_ be done. It should read like this: return_type const quarter = 0.25; We should fix that.

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2015-01-28 22:41:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#issuecomment-71932295  

Thanks for the PR and for merging

---

## Comment 25

> Username: awulkiew  
> Created_at: 2015-01-28 23:49:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23735534  

@barendgehrels Why it can't be assumed that a class may not have a default ctor? It's a common thing! It may have any number of non-default ctors taking arbitrary types. I don't understand this reasoning.  
  
Furthermore we shouldn't assume that a default-initialization is zero-initialization.  
  
What is more, we already require that T must be initialized explicitly in other places. In your fix above (assignment instead of an explicit ctor call) it makes no difference because a ctor will be called (at some point on a path of implicit conversions). E.g. here:  
  
```  
return_type const zero = 0; // implicit conversion <=> ctor  
```  
  
The library must and do require that there must be some path of implicit conversions from double/int to T. Effectively it's requiring ctor taking double or some other type constructible from numeric constant. So why add another requirement (zero-initializing default ctor)?  
  
And why value-initialization is better than explicit construction? It's worse, because with explicit construction you see immediately what's passed, exactly like with `false` or `NULL`.  
  
Or maybe I'm missing something?

---

## Comment 26

> Username: brunolalande  
> Created_at: 2015-01-29 09:17:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23754659  

As far as I know, you choose the T() initialization when you want to say something like "if it's an object it's supposed to know how to properly initialize itself (and I'm not supposed to know) and if it's a fundamental type then I know I want it to be 0 (which the C++ standard guarantees)". Iterators are a typical example: if it's a raw pointer you want 0, if it's an object you want it to do whatever is needed for it to be ready for iteration. So you write T() (you'll see it everywhere in the STL).  
  
In the case you are manipulating something that's supposed to represent a scalar value, and you want it to "mathematically represent" X, then you are not really in the situation described above. You're saying "whatever you are, you are now equal to X". So you'll rather use T(X).  
  
This is all general guidelines of course.  
  
Bruno

---

## Comment 27

> Username: barendgehrels  
> Created_at: 2015-01-29 17:23:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23784895  

NOTE: apprently this gives errors for clang because elsewhere you have declared this as a friend.  
  
```  
"clang++" -x c++ -Wno-unneeded-internal-declaration -std=c++11 -O0 -g -O0 -fno-inline -Wall -g -DBOOST_ALL_NO_LIB=1 -I".." -I"../boost/geometry/extensions/contrib/ttmath" -I"../libs/geometry/test" -c -o "/Volumes/Data/kbelco/boost_testing/results/boost/bin.v2/libs/geometry/test/iterators/segment_iterator.test/clang-darwin-4.2.1/debug/segment_iterator.o" "../libs/geometry/test/iterators/segment_iterator.cpp"  
  
In file included from ../libs/geometry/test/iterators/segment_iterator.cpp:46:  
In file included from ../boost/geometry/iterators/segment_iterator.hpp:22:  
In file included from ../boost/geometry/iterators/detail/segment_iterator/iterator_type.hpp:27:  
../boost/geometry/iterators/detail/segment_iterator/range_segment_iterator.hpp:157:12: warning: class template 'range_segment_iterator' was previously declared as a struct template [-Wmismatched-tags]  
    friend class range_segment_iterator;  
           ^  
../boost/geometry/iterators/detail/segment_iterator/range_segment_iterator.hpp:94:8: note: previous use is here  
struct range_segment_iterator  
       ^  
../boost/geometry/iterators/detail/segment_iterator/range_segment_iterator.hpp:157:12: note: did you mean struct here?  
    friend class range_segment_iterator;  
           ^~~~~  
           struct  
1 warning generated.  
  
```  
  
Please fix before we can merge this to master...

---

## Comment 28

> Username: mkaravel  
> Created_at: 2015-01-29 21:22:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23803701  

Fix done.  
Also fixed a bug that was introduced by the usage of `boost::size` to initialize the `m_has_less_than_two_elements` member variable.  
See PR #203.

---

## Comment 29

> Username: awulkiew  
> Created_at: 2015-01-30 12:57:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/197#discussion_r23840407  

Exactly, this is also my point, it's even semantically more clear to initialize coordinates explicitly passing a value. But currently in many places value-initialization is used, so my question is why it was choosen? For me it adds unnecessary requirement for a coordinate type and is less clear. But maybe I'm missing something?  
  
In case my reasoning was correct, I propose to not use zero-initialization and initialize objects with constants explicitly when a representation of a specific scalar/numeric value is needed. This is valid not only for coordinate types.

---
