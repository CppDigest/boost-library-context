# #323 Fix bug in Boost.Geometry's set operations for areal geometries [Closed]

> Username: mkaravel  
> Created at: 2015-08-27 22:01:34 UTC  
> Updated at: 2018-07-31 00:32:29 UTC  
> Closed at: 2018-07-31 00:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/323  

#### The problem  
  
Suppose we have two areal geometries A and B and we compute their union C; then C may not be valid, where validity is understood as the outcome of the `bg::is_valid()` function on the union output.  
  
Let me give a few concrete examples before explaining the problem:  
- Consider the two areal geometries in test case 36 of our unit test suite for union:  `POLYGON((1 0,0 3,4 2,1 0))` and `POLYGON((1 5,5 5,4 2,3 3,2 1,1 2,1 5))`. The output is a polygon that consists of a single exterior ring, which up to now we have considered as okay. The fact is, however, that such a polygon is invalid because the exterior ring has a self-intersection, which is exactly what triggers the validity test to fail. Instead, what we should have returned is a polygon with one interior ring that touches the exterior ring of the polygon at a point.  
- Consider the two areal geometries: `POLYGON((0 0,0 40,40 40,40 0,0 0),(10 10,30 10,30 30,10 30,10 10))` and `POLYGON((5 15,5 30,30 15,5 15))`. The output is a polygon that consists of a single polygon with a single hole, and this hole has a self-intersecting point that is actually a self turn of type m:u/u. The self intersection of the interior ring is the one that triggers the validity failure in this case.  
  
The common theme behind these two test cases (and many many more that can be easily constructed) is that u/u turns are discarded during the turn traversal phase. This is not a bad choice, and this is not the root of the problem. The problem is that the rings computed as part of the traversal may (and can as these examples show) have self intersections of two types t:u/u and m:u/u, which are precisely the turns that were discarded during the traversal phase. So the natural question that comes up is whether the traversal should actually take into account the u/u turns. My answer is no; there are u/u turns that should indeed be discarded (two disjoint polygons that touch each other at their exterior rings produce a u/u turn that should not be taken into account), but there are u/u turns that need to be considered, like in the example I have above. The _problem_ is that we cannot really determine which u/u turns to look at and which not at the traversal phase, and this is due to the fact that in order to determine which u/u turns need to be considered and used depends on global information about the geometries unioned, rather than local information available at the time of traversal.  
#### The solution implemented  
  
The solution proposed in this PR is a post-processing step that takes care of these issues.  Recall that the problem is that a ring computed during traversal can have m:u/u and t:u/u turns. The goal is to replace such a ring by a collection of rings that do not exhibit such turns (i.e., by a collection of simple rings). To resolve this I have implemented a two stage process:  
1. Given some ring R, I compute all its m:u/u turns and insert the corresponding points in the _middle_ of the ring segments that these turns appear. The reason for doing this is that in the new ring R (with the turns inserted at the right place), all turns have now become t:u/u turns. See also next item.  
2. Given some ring R with only t:u/u turns, we split it into a series of new rings that are all simple (and touch each other only at vertices). These new rings are now the basis for the exterior rings and the holes of the polygons to be computed by union.  
  
To do step 1 I actually do compute the self turns of the ring and create a new ring with all the proper vertices, including the m:u/u turns.  
  
For step 2 I have implemented the following algorithm.  
1. Check if R has t:u/u turns. This is not done with `bg::self_turns`, but rather using an `std::set` (as a dictionary more or less); basically I am using `std::set` as a way to detect if a point has already been detected or not).  
2. If R has u/u turns, split it into two parts/rings (using the common points found), and recursively continue on the two rings. Suppose that R has a duplicate vertex at indices `pos1` and `pos2`, the splitting is done by putting all points from `0` to `pos1-1` and `pos2` to `size()-1` into one ring and points between `pos1` and `pos2-1` into another (for closed rings there is something more to be done, but this is a technicality).  
   Otherwise, output R to some output ring collection.  
  
On the implementation side, and in order to avoid unnecessary copies, I am using internally a class for representing rings as a doubly-connected list (rather than a random-access range); I will call this ring a dcl-ring. This representation allows to do the splitting of a dcl-ring into two dcl-subrings in constant time and also exploits the storage allocated for the original dcl-ring. The reason that the splitting part is implemented recursively also avoids copying such dcl-rings; a stack or queue based implementation would require inserting dcl-rings into the stack or queue, so I wanted to avoid that.  
  
At the end of this procedure we have, instead of a single ring R, a collection of rings that are all simple and touch each other at what used to be the t:u/u turns of R. This new collection of rings is then passed to `bg::detail::overlay::assign_parent` and then to `bg::detail::overlay::add_rings` in order to finish up the construction of the union.  
#### Unit testing  
  
As part of this PR I needed to modify the expected output of some test cases, and I have added a few test cases that exhibit the issue.  
#### Union vs. Intersection  
  
There are similar issues with `bg::intersection()`. These are not addressed as part of the initial submission of this PR. However, the `bg::detail::overlay::split_rings<>` class that implements the post-processing step discussed above does take the overlay type (union or intersection) as a template parameter and will allow for handling intersections as well. At this point, the intersection-related instantiation of `bg::detail::overlay::split_rings<>` does nothing.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-08-27 22:15:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135570915  

Continuing the description of the PR, it might indeed be possible to implement the splitting in a non-recursive manner (using a stack) and by performing minimal copying. I will experiment on that while waiting for the review.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-08-28 07:42:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135663638  

Please add performance measurements. How does this extra step influence performance?  
Second, I think (but not work it out) that it is possible too to generate the traversal output in a different way. This step is then unnecessary. But I'm not yet sure about this. Could you provide some a drawing of the problem (is that possible here? By e-mail is also fine)

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-08-28 07:51:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135664815  

I will do performance tests, no problem. But here the issue is primarily that of correctness, and what I would like (before going for performance tests) is to make sure we have decided on the proposed method of attack. I would really like to hear your proposal about traversal, or at least a preliminary description of it.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-08-28 07:51:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135664875  

I have sent you personal emails regarding the drawings.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-08-28 08:09:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135667411  

@barendgehrels Please note the point that I make about u/u turns: some u/u turns need to be taken into account and some others need to be discarded; deciding which ones ot use and which not really depends (at least that is how I understand it) on global information about the geometries, and cannot really be decided while performing the traversal, which only looks at geometries locally.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-08-28 10:07:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135726376  

The `split_one_ring` method is now implemented in a non-recursive way; see commit 19d2684.  
The amount of copying is constant per splitting operation, just like what was the case with the recursive implementation.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-08-28 20:10:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-135876593  

As for the approach. Currently I don't have an idea how this could be done in a different way than a postprocessing step. Exactly because spatial relation of a vincinity of a u/u turn is the same in case of touching geometries and geometries forming a hole after union.  
  
As for the implementation, in general I think that storing Points in lists is not a good idea. It's highly probable that using vectors and copying Points would still be faster. It's because the dominant operation in the case of a list is the iteration while coppying the Points to the output, with complexity O(N) when most certainly many cache misses will occur. On the other hand, in the case of a vector we have more copies but of contigeous blocks when there should be relatively small number of cache misses. Of course ideally the difference of performance could be measured for several approaches.  
  
Personally I'd keep all of the Points of a Ring in one vector and recursively only pass the iterators representing interesting SubRanges into the deeper levels of recursion. Or maybe even better, if the Ring points were stored in one vector and the recursive splitting routine were always splitting the last SubRange in a Ring, then the number of copies of elements would be limited to the minimum. After a SubRange from the end of a Ring were handled and copied into the output the Ring could be resized (SubRange removed) and the process repeated. Processing the elements from the back of a vector should be very fast.  
  
I'm also wondering if it would be possible to get rid of the std::set AFAIU used to find a u/u turn corresponding to a SubRange of a Ring. Basically I think that the same thing that the one described above for Points could be done for turns. So, a range of u/u turns, sorted by segment id and fraction, corresponding to the Ring divided and traversed recursively, together with the Range of Points representing a Ring.  
  
Or is there something I don't see?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-08-29 23:06:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136060552  

@awulkiew I do understand your point about using or not using a list. There is also a choice here to be made as to whether it is better to implement the splitting recursively or not. The answers to these questions, indeed, could only be answered via benchmarking.  
  
Regarding your suggestion to use iterators to represent sub-ranges: I am not sure what you have in mind and how this would make the solution simpler, easier or faster. The ranges that we have here have **_circular**_ structure and I think this is main problem in implementing what you suggest.  
  
Let me try to convince you my using an abstracted example. Suppose I have the circular sequence of 18 letters (by circular I mean that these are the nodes of a graph and the last letter is connected to the first): `{a, b, c, d, e, f, d, g, h, i, g, k, l, m, n, c, o}`. In analogy to the geometric problem, this would represent an open ring produced by the traversal. The analogue of the splitting I want to do for rings is to split the letter sequence above into simple sequences (no duplicate points), and the wanted outcome is the collection of sequences `{{a, b, c, o},{c, d, g, k, l, m, n},{d, e, f},{g, h, i}}`. Suppose now that I do the search for duplicates as in the code, that is I scan from left to right and stop when the first duplicate is found. How we do the search for duplicates is important, but it also important to understand that any strategy for searching duplicates has a trade-off in terms of time and easiness, and also that any strategy can also have a counter-example for which this strategy can behave badly. So for the purposes of this example, let's stick to the strategy used in the code and explained above. From now on let me assume that the letters are given in an array (or range if you want), starting from 0. So the initial range is `[0..17]` and the first search for duplicates will reveal that I have duplicates at positions `3` and `6`. This means that `[0..17]` will be split in two, namely `[0..2] U [6..17]` and `[3..5]` (`U` here represents union in the mathematical sense). So immediately we see that representing one of the two circular sequences needs two index intervals (or two iterator intervals if you want). As to how the splitting is actually done, I also follow the convention in the code. The sub-range `[3..5]` will not be split any further, so let's focus on the other one. Searching for duplicates in `[0..2] U [6..17]` will return by saying that there are duplicates at positions `7` and `11`. So we need to further split this sub-range into two, where one is `[7..10]` and the other will have to be `[0..2] U [6] U [11..17]`. The latter sub-range contains three index intervals now, so even more complicated than the previous step. The sub-range `[7..10]` will not be split any further, while in the next step we need to search for duplicates in `[0..2] U [6] U [11..17]`. This will return by saying that we have duplicates at positions `2` and `16`, which means that will have to consider two new sub-ranges `[0..1] U [16..17]` and `[2] U [6] U [11..15]`. It is my understanding that in its full generality (and this is something that **_can**_ happen in geometric examples), the sub-ranges we need to consider can be more and more complicated, and in order to represent them we will need to consider a lot of indices/iterators; basically the _interesting_ sub-ranges, as you mention them, can have a non-constant number of index intervals, and this makes me hesitant to accept that using indices/iterators will really solve the problem.  
  
Finally, regarding your comment about u/u turns: remember that at the point where I do the splitting the only u/u turns that I have are really t:u/u turns, so we are really talking about duplicate/repeating vertices of the ring we are considering. Fractions have no real significance here (they are all either 0 or 1), and I have to admit that given what I described above about sub-ranges, it is not at all obvious to me how to exploit them the way you suggest.  
  
Please let me know if I have misunderstood something in your proposal.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-08-30 01:28:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136069936  

Thanks for the detailed explanation!  
  
Is my understanding correct that the letters represent Points (so letter equality is spatial equality) and that duplicating letters are the Points corresponding to the t:u/u?  
  
Indeed, the processing of Ranges is slightly more complicated than I thought. It's not possible to simply cut the SubRanges from the back. Because they are circular you must also cut the SubRange or rather move the front.  
  
You're also right that fractions has sense only in the case of m:u/u turns. When I was thinking about the turns Range the picture wasn't entirely clear. One turn represents two Points and it's probably possible to have several u/u turns on the same Point, so e.g. four `d`s. But in this case the SubRange is still the shortest one between the turn Points so it probably doesn't matter how many same Points are there. Basically my point was that it's not needed to store Points other than the duplicates, so Points representing u/u turns. It's because those are the only places where the Ring may be split, or in other words a SubRing may be cut off. In this scenario the input would look like this: `{cddggc}`. Then it makes sense to just store this small range in a vector and erase the SubRanges. And now starting from the end it's only needed to get rid of the wrapping `cc` range first and then gg and dd. And at the end collect all of the Points between the ranges, so `c-d`, `d-g` and `g-c`. Actually this looks like a linear operation, maybe it wouldn't be needed to erase or split anything, but instead start from the begin, gather `d-d`, `g-g`, wrapping `c-c` and then the rest? An interesting thing is that at the last stage, when there was `cc` (`{c....c}`) SubRange to handle, conceptually it'd represent two SubRings, the one between `c-c` which would be this SubRing made from the Points between cut-out SubRanges, and the complementary wrapping SubRange. Or am I missing something? After decomposing this algorithm like that it seems to be simpler than it initially looked. So maybe it would also be possible to linearly handle the Range of Points like that, without any erasing nor splitting? It seems that it could fail only if the Ring had self-intersection Points, and therefore overlapping SubRanges, but this is true also in the case of the original algorithm.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-08-30 13:40:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136138860  

Using `std::set` for lookup is basically ok but ideally it'd be good to get rid of it. AFAIU they (turn-Points Range) could be generated directly from the turns. After `get_turns()` call we exactly know where in a Ring the u/u turns are located, where the additional Points must be created, which Points correspond to each other, etc. We know how to spatially compare them, so we can just iterativaly process this Range. However several SubRings may touch each other on one Point, so using the analogy from the previous comment we could have more than 2 `d`s. I'm not sure if it would "just" work if the turn-Points Range mentioned in the previous comment was simply created using turns and processed. If not then indeed we'd need some kind of lookup (`std::set`) or first sort the `std::vector` using the same `less` compare and process the `equal_range`s preparing them to work correctly with the algorithm mentioned in the previous comment.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-08-30 13:49:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136139458  

I'm also thinking about a possible numerical consistency. Consider we have 3 SubRanges touching themselves on one Point, so 3 equal Points on a Ring. AFAIU in this case 4 u/u turns should be generated by `get_turns()`. Now suppose we have Rings' Points very close to each other but not the same. It'd be theoretically possible to have Point1 equal to Point2 and Point2 to Point3 but in the same time to have Point1 NOT equal to Point3. I'm guessing this could cause some problems no matter if `std::set`, sorted `std::vector` or Rtree were used. For now I don't have an idea how to handle it, assuming this problem could indeed occur.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-08-30 14:02:33 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38271900  

Is this C++03 move emulation? :)  
Is `RingType` adapted to Boost.Geometry Ring concept or `std::list<>`? I'm asking because Rings aren't required to implement `swap()`. So it seems that this code requires STL container. If the second part of my question is true then maybe it'd be a good idea to simply take `std::list` as parameter?  
  
To make it even more clear, you could implement `move_on_top()` or `move_back()`. Similar algorithm is used below.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-08-30 14:10:03 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38271953  

`top_ring` is used after `stack.push()` this may cause SEGFAULT since during push the stack may be resized so all elements copied into a different location. The `top_ring` should be split first and then the result pushed or moved using this function I mentioned in a different commit. The algorithm would also be more clear this way.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-08-30 14:14:17 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38272005  

Should `Ring&` be `const`?

---

## Comment 15

> Username: awulkiew  
> Created_at: 2015-08-30 14:15:17 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38272024  

Here too, not Boost.Geometry Ring but STL container is required. Is that correct?

---

## Comment 16

> Username: awulkiew  
> Created_at: 2015-08-30 14:17:45 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38272047  

If I understand correctly the purpose of this code is to `insert()` a Point if not found. `std::set<>::insert()` does exactly this returning `std::pair<iterator, bool>`. Then you have one less lookup call (`find`/`insert`).

---

## Comment 17

> Username: awulkiew  
> Created_at: 2015-08-30 14:20:35 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38272063  

Is the choice of an operation really a matter of correctness?

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-08-30 21:50:19 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38276391  

Essentially it is, yes.  
The RingType is not a model of the Boost.Geometry Ring concept. It is a ring that:  
- is a model of a bidirectional traversal range.  
- has a `split_at(pos1, pos2, other_ring)` method implemented, that allows to split it in two sub-rings using elements at positions `pos1` and `pos2`; the current ring is shortened and the rest is copied to `other_ring`.  
- supports a `swap(other_ring)` method.  
  
A ring type that satisfies these requirements is the `ring_as_dcl<>` class implemented in the same file.  
  
I have implemented `move_on_top()`; see commit 9b50df1. Indeed is makes the code more clear. Thanks for the suggestion.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-08-30 21:51:22 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38276405  

Good catch! Thanks.  
Implemented as suggested in the comment. See commit 9b50df1.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-08-30 22:23:28 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38276762  

Yes it should be `const` in the sense that the ring is not modified. On the other hand I need the `Iterator` to be a mutable iterator of the `Ring` type which means that internally I would have needed to convert a const iterator to a mutable iterator which is not possible.  
  
What I did was to make `ring` a `Ring const&` and internally convert it to a non-const ring so that the iterators passed to `pos1` and `pos2` are of the correct type. See commit e542d0d.

---

## Comment 21

> Username: mkaravel  
> Created_at: 2015-08-30 22:24:31 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38276784  

Yes, `Ring` here is the ring described in another comment. Basically it is `ring_as_dcl<>`.

---

## Comment 22

> Username: mkaravel  
> Created_at: 2015-08-30 22:25:35 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38276792  

Done. See commit 9b50df1.

---

## Comment 23

> Username: mkaravel  
> Created_at: 2015-08-30 22:30:37 UTC  
> Updated_at: 2015-10-16 09:47:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#discussion_r38276861  

Maybe the name is not the best one.  
  
Here is what is happening: I am having the turn `t`, which is of m:u/u type, and I want to get back the segment identifier and the fraction corresponding to this turn, and I want to get these two via the proper operation related to this turn. However, I am interested in the segment identifier and the fraction for the segment that the turn point lies in the _interior_ of the segment (so the fraction cannot be either 0 or 1), rather than the segment identifier for which the turn point is an endpoint of the segment. So the **_correct**_ operation I am interested in is the one for which the fraction is neither 0 nor 1, hence the name of the method.

---

## Comment 24

> Username: mkaravel  
> Created_at: 2015-08-30 22:33:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136210816  

The problem with duplicate points is that they can be nested. It is not obvious to me how to make it a linear-time operation, but I will think about it.

---

## Comment 25

> Username: mkaravel  
> Created_at: 2015-08-30 22:37:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136211296  

After the call to `get_turns()` I need to add points into the ring, and this messes the segment identifiers of the remaining turns a lot. I do not see how I would be able to get the info I want by just looking at the turns already computed initially. I also have the impression (but I need to verify this), that by adding the m:u/u turn points to the initial ring, there may be new turns that come up and I need to accommodate for those somehow as well.

---

## Comment 26

> Username: jeremy-murphy  
> Created_at: 2015-09-01 02:12:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136552676  

On the performance considerations of using `std::set` for Ring iterators, using a red-black tree seems like overkill but maybe it is necessary unless you're willing to restrict the Ring concept to not just random access but _contiguous_ elements (e.g. vector good, deque bad). Then presumably you could use a simple bitset on the ordinal value of iterators to check for duplicates?

---

## Comment 27

> Username: barendgehrels  
> Created_at: 2015-09-02 07:44:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-136964750  

As written in a private e-mail, it most probably can be done using the turns only. So this post processing is not necessary and should be omitted.  
  
So basically some u/u turns should be discarded, others should not. u/u turns connecting two polygons at one corner without any other interaction should still be discarded. u/u turns connecting two polygons in such away that there is one connected interior should not be discarded.  
  
The information in turns is local but contain travel information to next IP. Using that information it is known if the interior is connected.   
  
Consider a union of multi polygons P and Q, having a u/u turn. Take one of the rings part of that turn (e.g. P) and check if on that ring are other turns. If not, the u/u turn should be discarded. If there are other turns, and it is a non-u/u turn, check the operations and travel via the union operation (or continue operation) to the next IP, indicated by `travels_to_ip_index`. For that turn, do the same, etc.  until you either reach Q or reach P again. If you reach P again, then the interiors are disconnected and the specific u/u turn should be kept discarded. If you reach Q the interior is connected and the u/u turn should be part of the normal turn/traverse information. It should then be inserted in the sequence.  
  
I will come back with more info on mutually touching interior rings.  
  
So please don't merge this PR - because if this alternative approach works, it will be more efficient and less memory consumptive.

---

## Comment 28

> Username: mkaravel  
> Created_at: 2015-09-04 22:53:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-137874299  

The same approach is now used (within the scope of this PR) for `bg::intersection(A, A)`; see commits f8a0d93 and 1f2bbed.

---

## Comment 29

> Username: mkaravel  
> Created_at: 2015-09-05 23:09:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-138006262  

Commit f8a0d93 also changes (in the correct direction) how `bg::difference()` also works: self-intersecting rings produced during the traversal are also split.

---

## Comment 30

> Username: mkaravel  
> Created_at: 2015-10-16 09:54:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-148671048  

A few comments about commit 0770716:  
This commit adds a preprocessing step to the overlay algorithm. The preprocessing step computes the self turns of the two geometries and inserts in the geometries the points corresponding to any touch interior self turns they have.  
  
This preprocessing step is important for robustness reasons: suppose we have a geometry that has a touch interior self turn, the touch point `T` of which lies in the interior of some segment `AB` of the geometry. During a set operation the segment `AB` may be clipped to another segment `CD` containting `T`, where at least one of `C` and `D` lies in the interior of `AB`. By construction `T` must be a touch point that lies in the interior of `CD`, but numerically (i.e., since `C` and `D` may only be approximations of the geometrically true clip points) `T` may be considered as not a point on `CD`, which can then indicate that the computed geometry is invalid.  
  
The solution proposed in this commit basically inserts `T` in the geometry that has `T` as a self turns, and this enforces `T` to be present on `CD` when the set operation is performed.

---

## Comment 31

> Username: barendgehrels  
> Created_at: 2015-10-16 16:27:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-148761221  

Right - I think too that the preprocessing step is necessary.  
  
However, I still object to move this PR. First performance degradation measurements have to be specified, and alternatives has to be researched (as I did - more info later). No problem that this is not yet done - I understand everyone is quite busy. But it is just that I don't want to merge this PR because its influence on the whole design is so large.  
  
I actually prefer to close it (the merge button is quite easily pressed here...).

---

## Comment 32

> Username: mkaravel  
> Created_at: 2015-10-16 18:29:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-148795567  

@barendgehrels I have no intention to merge this PR until it is thoroughly discussed. Discussing alternatives is a must, and we have already started this discussion as you noted.  
I use this PR to keep track of my approach to the issues in set operations. I prefer to have them in GitHub as a PR so that you can easily comment and see the changes and potential solutions.

---

## Comment 33

> Username: ghost  
> Created_at: 2017-03-16 21:35:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-287198447  

Hello!  I'm just wondering about the status of this PR w.r.t. the current geometry HEAD.  Is it still relevant/ rebaseable?

---

## Comment 34

> Username: barendgehrels  
> Created_at: 2017-03-16 21:57:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-287203541  

No, it is not merged - and not completely relevant anymore. Validity is now implemented using a different way, though, to be honest, this was a lot of work and it is not yet completely finished. But still in progress.  
Thanks for your comment.  
I think we should close this review now - but the many discussions in it are still relevant.

---

## Comment 35

> Username: awulkiew  
> Created_at: 2017-03-16 22:05:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-287205349  

FYI, I keep a version of this fix in a branch in my fork which I merge with develop from time to time. The last merge I did was with boost-1.63.0 tag and currently it's mergeable with develop:  
https://github.com/awulkiew/geometry/tree/fix/mkaravel_setops  
However I didn't care much about code in `test/*` during merging, so it's possible that some expected results should be modified.  
  
So if you needed it badly you could use the code from this branch. If not then as Barend said we're working on it.

---

## Comment 36

> Username: awulkiew  
> Created_at: 2018-07-31 00:32:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/323#issuecomment-409055319  

All cases which this PR was fixing are now fixed in a different way so it is not relevant anymore. I'm closing it. Reopen it if needed.

---
