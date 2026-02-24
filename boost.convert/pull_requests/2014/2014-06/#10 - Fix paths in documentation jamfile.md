# #10 Fix paths in documentation jamfile. [Merged]

> Username: danieljames  
> Created at: 2014-06-25 14:26:01 UTC  
> Updated at: 2014-06-25 22:06:59 UTC  
> Merged at: 2014-06-25 21:38:09 UTC  
> Closed at: 2014-06-25 21:38:09 UTC  
> Url: https://github.com/boostorg/convert/pull/10  

I added Convert to the documentation build (at http://www.boost.org/doc/libs/develop/libs/convert/), but the CSS and image links aren't working. This should fix it by using the files in the super-project directly.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2014-06-25 22:00:04 UTC  
> Url: https://github.com/boostorg/convert/pull/10#issuecomment-47164581  

Daniel,  
  
Thank you for your help fixing my Jamfile. It's much appreciated. I merged  
your fix into all the branches including "master". Please let me know if  
there is anything else that needs to be done for "convert" to become a good  
Boost "citizen".  
  
Out of sheer curiosity... I see you changing:  
  
-path-constant boost-images : $(BOOST_ROOT)/doc/src/images ;  
+path-constant boost-images : ../../../doc/src/images ;  
  
That assumes one monolithic Boost directory. Given Boost is trying to move  
in the "modular" direction, would not that be sensible to rely on  
BOOST_ROOT or BOOST_BUILD_PATH instead? That way, with BOOST_ROOT set,  
separate modules would be still buildable outside the Boost tree. Just  
curious.  
  
Vladimir.  
  
On Thu, Jun 26, 2014 at 12:26 AM, Daniel James notifications@github.com  
wrote:  
  
> I added Convert to the documentation build (at  
> http://www.boost.org/doc/libs/develop/libs/convert/), but the CSS and  
> image links aren't working. This should fix it by using the files in the  
>   
> ## super-project directly.  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/danieljames/convert jamfiles  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/convert/pull/10  
> Commit Summary  
> - Fix paths in documentation jamfile.  
>   
> File Changes  
> - _M_ doc/Jamfile.v2  
>   https://github.com/boostorg/convert/pull/10/files#diff-0 (11)  
>   
> Patch Links:  
> - https://github.com/boostorg/convert/pull/10.patch  
> - https://github.com/boostorg/convert/pull/10.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/convert/pull/10.

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-06-25 22:06:59 UTC  
> Url: https://github.com/boostorg/convert/pull/10#issuecomment-47165282  

Maybe, but I don't think we can count on either BOOST_ROOT or BOOST_BUILD_PATH being defined.

---
