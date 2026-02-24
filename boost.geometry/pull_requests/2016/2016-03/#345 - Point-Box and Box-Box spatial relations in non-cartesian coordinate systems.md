# #345 Point/Box and Box/Box spatial relations in non-cartesian coordinate systems. [Merged]

> Username: awulkiew  
> Created at: 2016-03-09 04:56:23 UTC  
> Updated at: 2016-06-21 15:58:54 UTC  
> Merged at: 2016-03-09 16:50:28 UTC  
> Closed at: 2016-03-09 16:50:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/345  

This PR adds or changes the following:  
- support spherical/geographic CS in `box_in_box` strategy and enable it as the default one  
- support spherical/geographic CS in `point_in_box` strategy and enable it as the default one  
- fix `disjoint()`/`intersects()` for point/box in spherical/geographic CS (using tools implemented for `point_in_box` strategy)  
- fix `disjoint()`/`intersects()` for box/box in spherical/geographic CS (separate implementation)  
  
This PR changes the way how `within(point, box)` and `covered_by(point, box)` is calculated by default. The reason is that a Box in spherical and geographical CS is not a Polygon. The differences are:  
- the horizontal edge of a Box may be longer than 180 deg  
- the horizontal edge of a Box is not defined by geodesic but by small circle  
  
`point_in_box_by_side` is no longer used by default. It returns wrong result for Boxes with edges longer than 180 deg. It's because when a side is calculated the closer direction is picked so for those long edges (length in (180, 360]) some sides are the opposite than expected.  
  
This PR also fixes the ticket: https://svn.boost.org/trac/boost/ticket/11987  
  
Since `point_in_box` and `box_in_box` now support all CSes I plan to move them to `strategies/agnostic` directory. This is not a part of this PR to make the review of the code more convenient, to avoid displaying the unchanged parts of implementations as new ones on GitHub.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-03-09 08:31:19 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55485422  

Please please add parentheses here...

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2016-03-09 08:34:10 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55485658  

Parentheses missing

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2016-03-09 08:34:23 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55485681  

Space missing after !, parentheses missing

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2016-03-09 08:34:34 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55485701  

Parantheses missing

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2016-03-09 08:35:11 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55485763  

Also here, please check all your if's!

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2016-03-09 08:36:11 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55485856  

Sorry, I have no idea what you mean by bed / bing. Can you change the names to something more meaningful?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2016-03-09 08:38:18 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55486081  

So this code is duplicated, the only difference apparently being the last line? It must be possible to share more functionality?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2016-03-09 08:39:27 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55486177  

Duplicate ;

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2016-03-09 08:43:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#issuecomment-194185729  

Thanks for this PR! Overall it looks very good. Can you adhere more to the code conventions and check if the code duplication(s) can be factored out?  
I could not dive into the very details or test it. Can you give a short explanation of the substrategy?  
  
Did you plan to have this in 1.61? Would be very good, so we might merge it today allready and fix the comments later, that is OK for me.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2016-03-09 14:17:11 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55523366  

Those are the original names used in this file, see:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/cartesian/box_in_box.hpp#L43  
I think they are short versions of `box_contained_xxx` and `box_containing_xxx`. So if I wanted to change that I should change it consistently in the whole file. Should I do that? Do you prefer some specific name?

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2016-03-09 14:51:22 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55528622  

Ah yes that will it main. Did I create that? Anyway, the names you propose sound very clear, so yes, if you can change it, thanks

---

## Comment 12

> Username: awulkiew  
> Created_at: 2016-03-09 16:23:44 UTC  
> Updated_at: 2016-03-09 16:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55544370  

I'd prefer something shoter. I checked those names and they are less readable in expressions and make the lines a lot longer. So I'd prefer leaving them. Though indeed words `bed` and `bing` may be confusing. So if you strongly believed that they should be changed I'd prefer picking some different, short names, e.g. `box_ed_min`, `ed_min` or `min_ed`.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2016-03-09 16:43:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#issuecomment-194389582  

I refactored the code to avoid duplications and added missing braces.  
  
The `SubStrategy` was originally used as a method of injecting part of the code different for `within` and `covered_by`. I added template arguments in order to allow dispatching it by `Dimension` and CS tag. Then I specialized it for a family of spherical systems and Dimension == 0. This means that a longitude is handled differently than other cordinates.  
  
Yes, I'd like to have it in 1.61. The tests are passing locally with msvc14 and gcc 4.9.1. The only one issue that is left are the confusing names of arguments. And later I also plan to add a corresponding test for the rtree, update the release notes, etc. So sure, I think it may be merged.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2016-03-09 16:45:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#discussion_r55548332  

OK, no problem.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2016-03-09 16:46:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/345#issuecomment-194390980  

OK, thanks the adaptions and the explanation - it is clear now (also the remark about that only dimension==0 is handled differently). I'm OK with merging.

---
