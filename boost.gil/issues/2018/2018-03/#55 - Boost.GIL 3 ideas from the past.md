# #55 - Boost.GIL 3 ideas from the past [Closed]

> Username: chhenning  
> Created at: 2018-03-20 16:10:46 UTC  
> Updated at: 2018-03-30 10:39:31 UTC  
> Closed at: 2018-03-22 16:59:28 UTC  
> Url: https://github.com/boostorg/gil/issues/55  

These ideas are old and might be obsolete.  
  
* make sure not only integral types can the base type when creating a pixel type. See packed_pixel_t  
* outsource io_new's bit_operations  
* outsource io_new's io_devices  
  
* use boost::fusion for defining pixel and color spaces. See emails  
     * using variadic templates will help  
* make use of overlapping functionality with boost::geometry, like point type registration. See Andrew Hundt's email from 2013/03/13  
* pixel should have a begin() and end() members.  
* make Alignment a template parameter for image class  
* make use of c++11 features, like delegating constructor  
  
* look at the cairo proposal: https://groups.google.com/a/isocpp.org/forum/#!msg/graphics/UfvaqOyQbQ4/mv0CJS2mRtYJ  
* reference implementation: https://github.com/mikebmcl/N3888_RefImpl  
* pixman   
* http://developers.slashdot.org/story/14/01/04/2115249/cairo-2d-graphics-may-become-part-of-iso-c

---

## Comment 1

> Username: mloskot  
> Created at: 2018-03-20 16:45:38 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374670508  

I've taken the liberty to make the title and clearer, searchable and here too https://github.com/boostorg/gil/projects/2   
  
Current `master` and `develop` are at Boost.GIL 2, correct?

---

## Comment 2

> Username: chhenning  
> Created at: 2018-03-20 16:54:44 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374674001  

Yes, I think so.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-03-20 18:06:30 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374699874  

Right, thanks for clarification.  
  
It seems a bit confusing to not to refer to Boost versions, unless there is `<boost/gil/version.hpp`, like in [boost/beast/version.hpp](https://github.com/boostorg/beast/blob/develop/include/boost/beast/version.hpp) or [boost/spirit/version.hpp](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/version.hpp) and others.

---

## Comment 4

> Username: chhenning  
> Created at: 2018-03-20 22:08:23 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374773927  

I have added version.hpp and raised the develop version to 2.2. Indicating the new extensions, like io and toolbox.

---

## Comment 5

> Username: mloskot  
> Created at: 2018-03-20 22:13:26 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374775139  

Sounds good!

---

## Comment 6

> Username: mloskot  
> Created at: 2018-03-20 23:07:10 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374787558  

> make use of c++11 features  
  
Although I would welcome complete switch to C++11 that opens possibility to refactor/simpliy the library implementation, I think this is too early to set requirement of C++11:  
  
- Does Boost allow to bump minimum requirement for an existing library? I'm not sure.  
- When that should happen, before or after the new IO is released? I'd vote for after.  
  
/cc @stefanseefeld

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-03-21 03:15:32 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374823587  

I'm not sure there are any fixed rules as to whether we are allowed to make C++11 a requirement. Let's discuss this based on specific goals: can you give a specific example where using a C++11 feature would help ?  
I'm reluctant of adding dependencies on other boost libraries (in particular if they are not part of the core set of libraries, such as `boost::fusion`).

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2018-03-21 03:26:09 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374824714  

yeah, definitely after The Grand Merge, which will happen after the current release is complete. I'm confident I'll have the documentation refactoring complete by then...  
  
Any news on the checksum issue with variant=release ?

---

## Comment 9

> Username: mloskot  
> Created at: 2018-03-21 09:21:50 UTC  
> Updated at: 2018-03-21 14:41:32 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-374875459  

@stefanseefeld   
> I'm reluctant of adding dependencies on other boost libraries (in particular if they are not  
> part of the core set of libraries  
  
I agree, unless optional.  
  
> Any news on the checksum issue with variant=release ?  
  
Although cause of the problem is still a puzzle, I've made small progress and pin-pointed area of Boost.GIL where the issues happens. I will e-mail soon this week.  
  
@stefanseefeld   
> Let's discuss this based on specific goals: can you give a specific example where using a C++11 feature would help ?  
  
Get rid of boost::lambda, boost::bind ([good idea in 2005, bad idea in 2015](https://www.youtube.com/watch?v=zt7ThwVfap0)) for saner compiler errors, at least, I hope.

---

## Comment 10

> Username: chhenning  
> Created at: 2018-03-22 15:33:56 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-375350073  

Moving this to a wiki.

---

## Comment 11

> Username: chhenning  
> Created at: 2018-03-22 15:51:59 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-375356756  

https://github.com/boostorg/gil/wiki/gil-3-Ideas

---

## Comment 12

> Username: mloskot  
> Created at: 2018-03-22 16:13:45 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-375364522  

Closing?

---

## Comment 13

> Username: chhenning  
> Created at: 2018-03-22 16:42:44 UTC  
> Updated at: 2018-03-22 16:42:54 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-375374606  

Issue is not the format I think. A wiki seems better.

---

## Comment 14

> Username: mloskot  
> Created at: 2018-03-22 16:57:52 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-375380076  

Yes, so my question was if this shouldn't be closed, or you want to keep it open?

---

## Comment 15

> Username: mloskot  
> Created at: 2018-03-30 10:39:30 UTC  
> Url: https://github.com/boostorg/gil/issues/55#issuecomment-377499219  

I think I broken the link by renaming the wiki page, here it is  
  
https://github.com/boostorg/gil/wiki/Boost.GIL-3-Ideas
