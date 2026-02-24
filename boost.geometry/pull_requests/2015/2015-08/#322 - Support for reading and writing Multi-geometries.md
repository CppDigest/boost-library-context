# #322 Support for reading and writing Multi-geometries [Merged]

> Username: meastp  
> Created at: 2015-08-26 15:45:42 UTC  
> Updated at: 2017-02-26 19:09:50 UTC  
> Merged at: 2017-02-26 19:09:50 UTC  
> Closed at: 2017-02-26 19:09:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/322  

Adds support for Multi-geometries in the WKB data format.  
  
This is the first draft, and a (better) version of the svn patches for Multi-geometry WKB support. I need help with the equals-algorithm used for the tests, as they currently only check the number of geometries (in the multi-geometry).  
  
@mloskot

---

## Comment 1

> Username: meastp  
> Created_at: 2015-08-26 18:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-135129143  

When implementing write, I noticed that the only way to get the Linestring type from a MultiLinestring that I could find, was to use range_value<T>. Is this correct?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-08-26 18:28:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-135131526  

Yes.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-08-26 18:29:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-135131867  

I have to admit that it might not be the only way, but it is definitely the typical way we get the linestring type from the multilinestring type.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-08-26 18:59:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-135139734  

Thanks!  
  
Actually to get a reference to an element of a Range, because I think this is what we need in most cases, more correct would be to use `range_reference<>`. Or pass a result of `boost::begin(rng) + index` or `bg::range::at(rng, index)` into a function tamplate taking a true reference and let the compiler handle it. I'm encouraging to use `range_reference<>` because some users already mentioned that they return non-true references in some cases. Even if it is not fully correct because then a Range iterator becomes an InputIterator and we require RandomAccessIterators, nevertheless in general Boost.Geometry currently works well with non-true references.  
  
As for the `equals(MultiPoint, MultiPoint)` which AFAIU is not implemented yet. AFAIR the `equals_by_collection` works correctly only for Areal geometries, it's interesting that it compiles for MultiPoints. I think it would give wrong results if there were duplicated Points in the MultiPoint (AFAIR it's allowed), e.g. for  
  
```  
MULTIPOINT(0 0, 1 1)  
MULTIPOINT(0 0, 0 0, 1 1)  
```  
  
the result of `equals()` should be `true`. But I think `relate()` is implemented for them now so you could try using `equals_by_relate` instead. Point/MultiPoint and MultiPoint/Point are also missing. And then also tests would be needed. Would you prefer if we handled missing `equals()` variants?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-08-27 08:40:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-135341840  

Having multiples of points in a multipoint is definitely allowed in the sense the multipoint is considered as valid. I agree with Adam: `equals(MultiPoint, MultiPoint)` should rather be implemented using `equals_by_relate`.

---

## Comment 6

> Username: meastp  
> Created_at: 2015-08-31 18:24:11 UTC  
> Updated_at: 2015-08-31 18:24:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-136453387  

@awulkiew @mkaravel Thanks! I removed the FIXME's (Linestring type from MultiLinestring) and made an attempt to fix the equals algorithm. It seems to work well as the tests now fail when a Point's coordinates are not equal (whereas before, the algorithm would only count Points).  
  
@awulkiew As I'm only using range_value<...>::type, I don't see how your comment applies. I'm probably missing something. :)  
  
More comments? :)  
  
BTW: before a merge, I'd want to rebase this branch and fix the commits if that's okay.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-09-02 16:40:18 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38554878  

Given that now `equals_by_relate` is used, is `num_points_check` still needed?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2015-09-02 17:13:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-137175195  

@meastp Indeed you were only asking about the way of getting the type of a single Geometry from a MultiGeometry. I went a little further, because I thought about accessing an object and storing a reference to this object. Sorry, I should rather say something like: "yes, this is the only way, but take care about the references and if you'd like to store a reference then don't use `range_value<>::type&` but rather `range_reference<>::type`".  
  
But I saw some problematic places in the WKB reader code. The problem is that the Geometries are used like STL Containers, but they shouldn't. AFAIU WKB reader should work for any Geometry, so any type adapted to a Concept, not only the Boost.Geometry models located in the `boost::geometry::model` namespace. Rings, Linestrings and MultiPoints are Ranges, not STL Containers. To access a type of an element or a reference `boost::range_value<>::type` or `boost::range_reference<>::type` must be used accordingly, not `value_type` and `reference` member types. For mutable Ranges of Points, `push_back()` or `resize()` member functions shouldn't be used because in general a Geometry may not define it. `bg::traits::push_back<>` and `bg::traits::resize<>` should be used instead, or one of the utilities defined in `geometry/util/range.hpp`, e.g. `bg::range::push_back()` which uses the trait mentioned above. This also means that Geometries in general won't work with `std::back_insertert_iterator<>`.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-09-02 17:15:22 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38558616  

This is a comment to the already existing code in a line above this one. Rings may not define `resize()` or `back()`. Please use e.g. `bg::range::resize()` and `bg::range::back()`.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2015-09-02 17:32:51 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38560489  

A MultiPoint may not have an interface of a STLContainer and in particular may not define `push_back()` member function, which means that `std::back_insert_iterator<>` may not work.  
  
However in various places of the library, e.g. in set operations like `union_()` `std::back_inserter(out)` is passed into the internals of the algorithm. This implies that an Output must define `push_back()` member function which means that this algorithm (and others) may not work well with an arbitrary MultiGeometry. Since similar code can be found in other places in the library it can stay as it is. Nobody complained about it so I'm guessing the users are either using STL Containers, Boost.Geometry models or just implement `push_back()` member function.  
  
We could think about a solution later, e.g. to implement our own `back_inserter`, but not as a part of this PR.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-09-02 17:34:05 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38560620  

Should be `boost::range_value<MultiPolygon>::type`.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-09-02 17:36:15 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38560844  

Should be `range::resize()` and `range::back()` and instead of `ring_type&` `range_reference<...>::type` or `ring_return_type<Polygon>::type`.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2015-09-02 17:37:17 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38560981  

`range_reference<...>::type` or `ring_return_type<Polygon>::type`.

---

## Comment 14

> Username: awulkiew  
> Created_at: 2015-09-02 17:41:56 UTC  
> Updated_at: 2016-09-05 18:21:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#discussion_r38561480  

Is it a good idea to cast a maximum value of `difference_type` into `uint32_t`? Isn't it a 64bit type on a x64 machine?

---

## Comment 15

> Username: meastp  
> Created_at: 2016-01-22 20:18:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-174035472  

@mkaravel   
  
@awulkiew : Finally I had time to address the comments. I have tried to correct all the comments, leaving back_inserter. I fully agree on writing against the Concept interface and not the (STL) implementation/model, of course.  
  
Please take a look, and see if this is ready to merge. Before a merge, I would like to rebase and cleanup the commits/PR.

---

## Comment 16

> Username: awulkiew  
> Created_at: 2016-01-23 13:12:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-174185415  

@meastp Thanks!  
  
I've prepared PR #340  adding `range::back_insert_iterator` and `range::back_inserter()` calling traits version of push_back. So let's wait for others to comment about it and if they agreed that it's ok you could replace `std::back_inserter()` with `range::back_insterer()`. I guess it could also be done later since now the users are most certainly defining `push_back()` member functions in their geometries because otherwise some of the algorithms doesn't work.  
  
I'm also not sure if assertion failure is a good method of reporting when the format may be not suitable to represent data which is too big, container has too many elements, etc. Shouldn't rather an exception be thrown in this case?

---

## Comment 17

> Username: meastp  
> Created_at: 2016-01-24 20:24:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-174338156  

@awulkiew Great! Just tell me when it is merged to develop, and i will rebase and change to range::back_inserter() :)   
  
What exception should I throw, and what message (if there is a string member)?

---

## Comment 18

> Username: awulkiew  
> Created_at: 2016-01-25 00:23:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-174356163  

It should be an exception defined in Boost.Geometry in this file:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/core/exception.hpp  
and should have a name probably corresponding to `std::length_error` and containing a word `exception` at the end because this is how all exceptions are called in the library, so e.g. `bg::length_exception`, `bg::invalid_length_exception` or some similar name.  
Do you have some preference?  
@barendgehrels what do you think?  
  
There is one more issue. Raw `assert()` should be avoided in Boost code, `BOOST_ASSERT()` or `BOOST_ASSERT_MSG()` should be used instead.

---

## Comment 19

> Username: awulkiew  
> Created_at: 2016-01-28 13:55:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-176194875  

@barendgehrels Do you prefer some specific exception name?

---

## Comment 20

> Username: meastp  
> Created_at: 2016-02-23 20:25:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-187887651  

@awulkiew @barendgehrels   
I can see that PR #340 was merged. Did you agree on an exception text?  
  
Should I use `BOOST_ASSERT()`/`BOOST_ASSERT_MSG()`, or an exception where `assert()` is currently used?

---

## Comment 21

> Username: mloskot  
> Created_at: 2016-03-03 11:09:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-191710746  

I'd like to follow @meastp excellent work, testing it, etc. So, I'm strongly interested in merging this PR.  
Is it ready?  
  
I also asked related question about extensions http://lists.boost.org/geometry/2016/03/3502.php as I'm interested in possibility to release the WKB I/O.

---

## Comment 22

> Username: awulkiew  
> Created_at: 2016-03-03 12:22:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-191732605  

If you ask me, I'm ok with merging the PR after the exception throw is added. This exception would be a part of an interface so we should choose the name wisely. You both are also members of Boost.Geometry community so your opinions are valuable. :)  
  
Furthermore we should wait for @barendgehrels.  
  
As for the name, I'm ok with both `length_exception` and `invalid_length_exception`, but if we decided to use one of them I'd prefer `length_exception` because it's shorter. If you have a different idea I'm open for suggestion. AFAIR `std::length_error` is thrown by STL only during resizing, when the new size is directly passed into a function, so it's an exception signalling that the argument is wrong. Here a container is also resized internally but an argument is geometry, so an exception should refer the geometry, i.e. indicate it contains too many points. `length_exception` might not be a good name, it might be confusing since length WRT geometry means something different. So it might be a better idea to be more specific and throw `write_wkb_exception`, so corresponding to `read_wkt_exception`.  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/io/wkt/read.hpp#L63  
What do you think?

---

## Comment 23

> Username: mloskot  
> Created_at: 2016-03-03 23:13:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-192012800  

I have seen `length_error` is used in lots of places, reported when vector/list/map/string would become too large.  
  
I might be misreading the C++11 standard, but there is this confusing statement that `length_error` is `logic_error` type with intention for "errors presumably detectable before the program executes", to report violations of implementation-defined limits. Whereas I see it in the standard library used in the actual runtime checks (`std::runtime_error` category).  
  
However, since `geometry::exception` is derived from the general base `std::exception`, its subclass `read_wkt_exception` seems outside of the typical C++ exceptions categories. Thus, to be consistent with what is currently defined and for symmetry, I would use `write_wkb_exception : public geometry::exception`.  
  
I hope my reasoning is clear and helpful.

---

## Comment 24

> Username: barendgehrels  
> Created_at: 2016-03-06 17:36:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-192941267  

Sorry for the late reaction. I'm OK with merging this. I am OK with adding the exception and write_wkb_exception sounds like the best choice for naming it.  
Thanks for this work!

---

## Comment 25

> Username: mloskot  
> Created_at: 2016-04-14 15:35:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-210006272  

@barendgehrels Thanks!  
  
@meastp I'd like to merge your PR. What do you think about the `write_wkb_exception` addition?

---

## Comment 26

> Username: meastp  
> Created_at: 2016-04-16 08:35:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-210770385  

@mloskot Great! I like the corrections for exceptions. I just haven't found time to do them yet :)  
  
Also, I would like to rebase the pr before merge, to clean the commit log.

---

## Comment 27

> Username: meastp  
> Created_at: 2016-06-29 18:33:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-229447233  

I have finally converted the asserts to use of exceptions (I added read_wkb_exception and write_wkb_exception). Hopefully it is now ready to rebase and merge? @mloskot

---

## Comment 28

> Username: awulkiew  
> Created_at: 2016-06-30 13:56:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-229665750  

Thanks!  
  
The new exceptions are added into `geometry/core/exception.hpp`, but WKB is not a part of official release. So either WKB should be moved from extensions or the exceptions should be moved to `wkb/write.hpp` and `wkb/read.hpp` like it is in case of `wkt/read.hpp` containing the definition of `read_wkt_exception`. Do you think WKB is ready to be moved from extensions and released?  
  
Actually it's not clear for me if the exceptions, even in the final release, should be defined in `geometry/core/exception.hpp` because IOs are optional, and as I mentioned in case of WKT the exception is defined in `wkt/read.hpp`. But maybe indeed all exceptions should be moved into a single file. Anyway, this is not a subject of this PR.  
  
Furthermore there is more information in `read_wkt_exception`, e.g. part of WKT string where the error was detected is included in the exception. But something like this could be added later if needed. Btw, is it possible to detect corrupted WKB?  
  
Regarding rebasing. There is one change in this PR made for `bg::equals()`, adding suport for Multi-Geometries. This should be done in a separate commit.

---

## Comment 29

> Username: mloskot  
> Created_at: 2016-06-30 14:28:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-229675015  

> is it possible to detect corrupted WKB?  
  
In general case, no, not really.  
In cases of substantial WKB degradation, it is only possible to verify whether byte stream following declared number of points is at least of length necessary to store `sizeof(double) * number of X + sizeof(double) * number of Y [+ sizeof(double) * number of Z]`

---

## Comment 30

> Username: awulkiew  
> Created_at: 2016-06-30 17:02:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-229722413  

Or if e.g. geometry IDs are invalid or incompatible with Geometry passed into `read_wkb()`. Speaking of which. I see that `read_wkb()` returns `false` in this case, while `read_wkt()` throws an exception. If we decided to move WKB IO from extensions at some point in the future it should be consistent with WKT IO (one way or another, though in reality there is only one way because WKT IO is already released).

---

## Comment 31

> Username: mloskot  
> Created_at: 2016-06-30 23:40:14 UTC  
> Updated_at: 2016-07-01 09:33:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-229818537  

On 30 Jun 2016 19:02, "Adam Wulkiewicz" notifications@github.com wrote:  
  
> Or if e.g. geometry IDs are invalid or incompatible with Geometry passed into read_wkb().  
  
Yes in BG case it makes sense.  
  
> Speaking of which. I see that read_wkb() returns false in this case, while read_wkt() throws an exception. If we decided to move WKB IO from extensions at some point in the future it should be consistent with WKT  
  
Good point. I agree.

---

## Comment 32

> Username: meastp  
> Created_at: 2016-07-05 19:41:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-230580982  

@awulkiew @mloskot Thanks -- I have moved the exception into parser.hpp. I will remember to move the bg::equals into a separate commit when rebasing. I think the error handling can be addressed in a separate PR, after this is merged..?

---

## Comment 33

> Username: awulkiew  
> Created_at: 2016-07-05 23:02:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-230627921  

@meastp Sure.

---

## Comment 34

> Username: meastp  
> Created_at: 2016-09-05 18:24:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-244795925  

@awulkiew @mloskot I have rebased. I think I've addressed all the issues so far. Is this ready to merge? :)

---

## Comment 35

> Username: mloskot  
> Created_at: 2016-09-06 08:38:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-244886203  

@awulkiew do you mind to merge? I can only hit the green button ATM, not sure if this is acceptable way to merge,

---

## Comment 36

> Username: meastp  
> Created_at: 2016-11-24 19:17:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-262834225  

Is there anything missing, preventing this from being merged? :)

---

## Comment 37

> Username: awulkiew  
> Created_at: 2016-11-24 19:48:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-262837208  

Ah, thanks for the reminder and sorry about delay. There are 2 things mentioned above which were not addressed i.e.:  
- throwing an exception on error instead of returning `false`  
- use of `bg::range::back_inserter` instead of `std::back_inserter`  
but I'm ok with merging them as they are now (it's still an extension).  
  
Barend what do you think?  
  
A side note, this PR enables `bg::equals()` for MultiPoints. Could it be merged now or should it be merged into develop after develop is merged into master for 1.63?

---

## Comment 38

> Username: meastp  
> Created_at: 2016-11-24 19:57:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-262838067  

I agree with fixing the back_inserter now if you want.  
  
Throwing exceptions will change the non-multi geometry wkb behaviour as well. Perhaps that could be done in a separate PR after this is merged?

---

## Comment 39

> Username: mloskot  
> Created_at: 2016-11-25 10:31:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-262927371  

Since I have failed to take care of completing the merge myself, I don't want to interfere with opinions of the current active maintainesr (Barend, Adam). However, AFAIU we agreed the exceptions can be improved in separate PR.  
  
@awulkiew Thank you for taking care of this PR!

---

## Comment 40

> Username: awulkiew  
> Created_at: 2016-11-25 13:25:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-262956941  

@mloskot Yes, I said that I'm ok with merging earlier. I should have mentioned it again, sorry for confusion. Yes, I'm ok with merging the PR as it is. Though I'd like to hear from Barend too.  
  
The biggest problem I have is related to the schedule of 1.63 release. Since not only the extension is modified but also `equals` I don't know if we should wait for develop being merged into master or could we merge it now and release the modified `equals` with 1.63. In other words should this change be treated as new functionality or as a bug fix.

---

## Comment 41

> Username: barendgehrels  
> Created_at: 2016-11-25 17:05:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-262998947  

Sorry that this process takes so long indeed. But yes, we cannot get it in 1.63 anymore, it is new functionality or at least a major change. It is not simply a bug fix. So let's plan to merge it as soon as 1.63 is released, to be sure we get it into 1.64

---

## Comment 42

> Username: barendgehrels  
> Created_at: 2017-02-20 21:30:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/322#issuecomment-281185526  

I'm OK with merging this - it is now certainly still possible for 1.64

---
