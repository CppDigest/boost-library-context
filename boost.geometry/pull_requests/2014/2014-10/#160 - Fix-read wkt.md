# #160 Fix/read wkt [Merged]

> Username: awulkiew  
> Created at: 2014-10-13 23:52:45 UTC  
> Updated at: 2014-12-16 22:30:55 UTC  
> Merged at: 2014-11-19 16:57:48 UTC  
> Closed at: 2014-11-19 16:57:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/160  

This PR changes the behavior of `read_wkt()` for open Rings to be consistent with `wkt()` output and in the same time backward compatible. `wkt()` always outputs the closing Point, even for open Rings to be WKT-standard-conformant. Without this fix `read_wkt()` naiively appends all Points passed in the input. This means that if a WKT generated with `wkt()` is loaded with `read_wkt()` into an open Polygon the last, duplicated Point is appended. This PR changes the `read_wkt()` in a way that it doesn't append the last Point of the input if it equals the first one.  
  
The interesting edge case is when there is not enough Points in the input (i.e. less than 3 for open Ring) Points are always appended. Though I'm not sure if this is needed. It of course may be changed to always ommit the last, duplicated Point.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-10-14 09:01:45 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18816073  

Is this a tab?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-10-14 09:04:31 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18816190  

What is the purpose of these lines?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-10-14 09:29:44 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18817234  

Those are spaces :) I aligned the parameters.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-10-14 09:31:06 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18817282  

This was a test which shouldn't be here, thanks.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-10-14 12:51:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#issuecomment-59037330  

@awulkiew Have you run the entire test suite in the algorithms directory?  
I remember I made some changes in the `for_each` unit test, that might be broken due to your changes (or maybe not....).

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-10-14 12:54:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#issuecomment-59037775  

@awulkiew You may disregard my last comment regarding the `for_each` unit test.  
The issue I refer to closed geometries, not affected by your changes.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-10-14 13:00:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#issuecomment-59038524  

A relevant issue: for closed geometries when we output their WKT description, we append the first point if it is different from the last.  
  
Suppose now that we modify the first point of a closed geometry, but not the last. This, of course, yields an invalid geometry, but when we output this geometry in WKT, the WKT description contains one more point than the original geometry.  
  
Personally, I do not like this behavior, and I would expect that we append the first point in the WKT only for open geometries. In the scenario I mention above, the WKT for the invalid geometry would be invalid, which is perfectly fine with me. "Fixing" the WKT for the invalid geometry might lead to undetected false positives, due to the way `bg::wkt` function currently works.  
  
Opinions?

---

## Comment 8

> Username: awulkiew  
> Created_at: 2014-10-14 15:04:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#issuecomment-59060483  

Yes, duplicating the Point only for open geometries is reasonable and intuitive. It's possible that the code in `wkt()` was some kind of shortcut.  
  
We could even consider throwing an exception is such situations, not only in `wkt()` but this could be a good candidate.  
  
I may of course modify `wkt()` but probably as a separate PR since this one is for `from_wkt()`.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2014-10-14 18:25:49 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18848325  

Until now, we did not align them like this. Just two x 4 spaces is OK

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2014-10-14 18:36:26 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18849090  

tag_cast should go into include

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-10-14 18:37:05 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18849146  

It was already like this, but basically point_type removes all const's and references from any type

---

## Comment 12

> Username: awulkiew  
> Created_at: 2014-10-14 21:48:43 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18861390  

bare_geometry Range is needed for range_size<> and closure<>

---

## Comment 13

> Username: awulkiew  
> Created_at: 2014-10-14 21:50:57 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18861501  

So are you saying that this is less readable for you and should be reversed?

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2014-10-14 22:02:17 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18862244  

Of course not. I'm indicating that you change indentation according to the current conventions to something you feel better, but is (until now) not done in the code.  
It is just because your comment that I noticed this. But it is good to be aware of this.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2014-10-14 22:03:06 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18862287  

I did not say that. But you don't need range_size (see other comment). So you don't need bare_type

---

## Comment 16

> Username: awulkiew  
> Created_at: 2014-10-14 22:12:37 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18862836  

Sorry, I updated the comment. I need it also for closure<>.  
And btw, using std::size_t instead of range_size<>::type for Ranges is not correct.

---

## Comment 17

> Username: awulkiew  
> Created_at: 2014-10-14 22:59:45 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18865217  

`point_type<>` uses `bare_type<>` which removes const and pointer, not references. This is why in the original code `remove_reference<>` was used as well. Do you think that the references should also be removed?

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2014-10-15 11:18:19 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18887287  

Good point. Yes, I think so, bare is bare... Cannot remember why we did not do that yet.

---

## Comment 19

> Username: awulkiew  
> Created_at: 2014-10-16 14:51:15 UTC  
> Updated_at: 2014-11-03 19:01:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#discussion_r18959815  

I added remove_reference to bare_type and used bare_type in closure and point_order, in develop branch in case this PR wasn't merged. And I removed unnecessary remove_reference<> from read_wkt.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2014-10-16 15:43:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/160#issuecomment-59382808  

I am fine with merging this PR. Thanks Adam.  
We should consider though fixing the behavior of `wkt()` wrt to (possibly) appending the first point for closed geometries (see comment above).

---
