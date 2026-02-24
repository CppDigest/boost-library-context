# #6 Update transform.hpp [Closed]

> Username: akumta  
> Created at: 2015-03-13 16:38:09 UTC  
> Updated at: 2015-09-01 15:44:17 UTC  
> Closed at: 2015-09-01 15:44:17 UTC  
> Url: https://github.com/boostorg/polygon/pull/6  

Update for ticket #11110

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2015-03-16 08:48:38 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-81509427  

Hi akumta, thank you for the pull request.  
  
From the user perspectivate (style consistency) it's better to change the names of all the enum values (e.g. WS to W_S) on Solaris.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-18 16:38:53 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-83050636  

The tests on Android also fails because of this. Surprisingly this is not caught by any of the Polygon's test but by Geometry. If you're curious, the test is `boost_polygon` on `geometry` summary page (see e.g.: http://www.boost.org/development/tests/develop/developer/CrystaX-NET-apilevel-21-x86-geometry-gcc-4-8-boost_polygon-variants_.html).

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-03-18 17:45:37 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-83085194  

I'm not sure how this issue should be addressed to be fully portable and doesn't hurt the users. I'm guessing that this may be a general problem with STLPort. So instead of changing the name only on Solaris the names should probably be changed on all NIX systems if STLPort was used. Or maybe on all systems other than Windows? Or maybe if architecture was x86?  
  
I'd discourage releasing a non-portable solution which would work on some platforms differently. An alternative would be to deprecate those 2-letter names. For users convenience they could be conditionally enabled with some macro definition.  
  
Another thing is that all of those names doesn't meet Boost/STL naming convention (see: http://www.boost.org/development/requirements.html#Design_and_Programming). Those should be lower-case names with words separated with underscore. So all of them could be renamed.  
  
I haven't found `transform` described in the documentation. This functionality also seems to be not tested. So there wouldn't be any regression if it was changed. Am I missing something? It looks like if it wasn't released. I'd say that it could be changed in any way.

---

## Comment 4

> Username: asydorchuk  
> Created_at: 2015-03-18 18:55:59 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-83121546  

I am a bit concerned with the current solution as well. If I define ES macro in polygon test it fails as well, however it depends where it's defined. If you include polygon headers before system headers (that contain conflicting directive) everything works (as in case of polygon tests), otherwise it doesn't (as in case of geometry tests).  
  
Polygon library was included a while ago and accepted in the state it is. Fixing naming at the moment is not the best practice. As you mentioned, the transform doesn't seem to be mentioned in the public interface of the library, so I am considering to exclude 2-letter names from ATR enum.

---

## Comment 5

> Username: asydorchuk  
> Created_at: 2015-03-18 19:04:45 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-83123501  

I removed two-letter names, fixed the polygon tests and merged the change to the develop branch.  
Within the next few days, going to check if this causes any tests for other Boost libraries to fail. If everything works as expected going to merge changes to master.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-03-19 01:33:16 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-83250738  

Thanks for the fix! I wasn't sure if it's public or not. In case if it wasn't, AFAIU now it's too late for merging breaking changes for 1.58, isn't it? Of course I agree that renaming the names could be problematic for the users. I hope that removing the 2 letter names won't cause much harm. A safe approach could involve hiding them behind some `#ifdef`, e.g.: `BOOST_POLYGON_ENABLE_DEPRECATED` or something similar.

---

## Comment 7

> Username: asydorchuk  
> Created_at: 2015-03-20 19:13:17 UTC  
> Url: https://github.com/boostorg/polygon/pull/6#issuecomment-84103565  

I used your recommendation and added BOOST_POLYGON_ENABLE_DEPRECATED directive. Once the tests for Geometry run smoothly going to merge to master.

---
