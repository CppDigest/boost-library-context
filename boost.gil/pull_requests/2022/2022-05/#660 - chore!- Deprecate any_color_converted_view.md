# #660 chore!: Deprecate any_color_converted_view [Closed]

> Username: marco-langer  
> Created at: 2022-05-04 19:07:51 UTC  
> Updated at: 2022-06-06 18:10:07 UTC  
> Closed at: 2022-05-26 06:54:58 UTC  
> Url: https://github.com/boostorg/gil/pull/660  

### Description  
  
I suggest to deprecate the function `any_color_converted_view` from `gil/extension/dynamic_image/image_view_factory.hpp`, because it has the exact same implementation as the function `color_converted_view`:  
  
```C++  
template <typename DstP, typename ...Views>  
inline  
auto color_converted_view(any_image_view<Views...> const& src)  
    -> typename color_converted_view_type<any_image_view<Views...>, DstP>::type  
{  
    using cc_view_t = typename color_converted_view_type<any_image_view<Views...>, DstP>::type;  
    return apply_operation(src, detail::color_converted_view_fn<DstP, cc_view_t>());  
}  
```  
```C++  
template <typename DstP, typename ...Views>  
inline  
auto any_color_converted_view(const any_image_view<Views...>& src)  
    -> typename color_converted_view_type<any_image_view<Views...>, DstP>::type  
{  
    using cc_view_t = typename color_converted_view_type<any_image_view<Views...>, DstP>::type;  
    return apply_operation(src, detail::color_converted_view_fn<DstP, cc_view_t>());  
}  
```  
  
According to the documentation of `any_color_converted_view`, the prefix `any_` was added to avoid an ambiguous overload resolution for GCC 3.4, which however, we do not have to support anymore with C++11.   
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2022-05-04 19:42:34 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1117779374  

Makes sense, thanks! Actually I wonder if we could remove it alltogether...

---

## Comment 2

> Username: striezel  
> Created_at: 2022-05-04 19:57:48 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1117820864  

> Actually I wonder if we could remove it alltogether...  
  
As far as I understand Boost's policy with regards to deprecation there needs to be at least one version / release where stuff that will get removed has to be announced as deprecated. Then the next release can actually remove that stuff.  
  
So it could be something like:  
  
* Boost 1.79.0: feature X is still present.  
* Boost 1.80.0: announce deprecation  
* Boost 1.81.0: remove feature X

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-05-18 07:50:48 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1129686655  

> Boost 1.80.0: announce deprecation  
  
Yes, we need to deprecate it first with a release note similar to this  
https://github.com/boostorg/gil/commit/8b1c2d3ea4eb5bf0a1e7e093862962313dd6c2bb

---

## Comment 4

> Username: striezel  
> Created_at: 2022-05-25 13:42:12 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1137256785  

> Yes, we need to deprecate it first with a release note  
  
I've just created a pull request with the corresponding release notes to get things moving: #678.

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-05-25 21:12:44 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1137853302  

@striezel AFAIU, this PR is to be closed in favour of #678 Correct?

---

## Comment 6

> Username: striezel  
> Created_at: 2022-05-25 23:24:44 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1137947955  

Yes, it can be closed.  
  
However, after the release of Boost 1.80 a new PR can (and should) be created that actually removes the then deprecated `any_color_converted_view`.

---

## Comment 7

> Username: marco-langer  
> Created_at: 2022-05-26 05:58:18 UTC  
> Updated_at: 2022-05-26 06:42:29 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1138183545  

In my PR here I also changed the implementation of the functions in order to remove the invocation of `apply_operation()`. This will be necessary anyway in order to complete #656 (actually I postponed further work on #656 and waited until this PR gets merged, because both have merge conflicts).  
  
#678 hasn't changed the implementation of the deprecated functions. But I can rebase #656 later on and change the implementation again if #678 gets merged.

---

## Comment 8

> Username: mloskot  
> Created_at: 2022-05-26 06:54:58 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1138223320  

@marco-langer  Good point. Thank you very much for your help!  
  
----  
  
I'm closing this PR and I'm going to merge @striezel 's #678.

---

## Comment 9

> Username: marco-langer  
> Created_at: 2022-06-04 04:02:12 UTC  
> Updated_at: 2022-06-04 04:06:57 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1146522669  

Since #678 got merged, I noticed that the deprecation warning is triggered with some compilers even if the function is not invoked at all:  
  
```C++  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
  
int main() {}  
```  
  
Compiled with gcc 10.2.1:  
```  
In file included from ../../boost/boost/gil/extension/dynamic_image/dynamic_image_all.hpp:14,  
                 from ../../boost/boost/gil/extension/toolbox/dynamic_images.hpp:11,  
                 from ../../boost/boost/gil/extension/toolbox/toolbox.hpp:13,  
                 from ../../boost/boost/gil/io/base.hpp:11,  
                 from ../../boost/boost/gil/extension/io/png/tags.hpp:11,  
                 from ../../boost/boost/gil/extension/io/png/read.hpp:13,  
                 from ../../boost/boost/gil/extension/io/png.hpp:11,  
                 from main.cpp:2:  
../../boost/boost/gil/extension/dynamic_image/image_view_factory.hpp: In function ‘typename boost::gil::color_converted_view_type<boost::gil::any_image_view<Types ...>, DstP, CC>::type boost::gil::any_color_converted_view(const boost::gil::any_image_view<Types ...>&, CC)’:  
../../boost/boost/gil/extension/dynamic_image/image_view_factory.hpp:388:71: warning: ‘typename boost::gil::color_converted_view_type<boost::gil::any_image_view<Types ...>, DstP, CC>::type boost::gil::any_color_converted_view(const boost::gil::any_image_view<Types ...>&, CC)’ is deprecated: Use color_converted_view(const any_image_view<Views...>& src, CC) instead. [-Wdeprecated-declarations]  
  388 |     return apply_operation(src, detail::color_converted_view_fn<DstP, cc_view_t>());  
      |                                                                       ^~~~~~~~~  
../../boost/boost/gil/extension/dynamic_image/image_view_factory.hpp:384:6: note: declared here  
  384 | auto any_color_converted_view(const any_image_view<Views...>& src, CC)  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~  
../../boost/boost/gil/extension/dynamic_image/image_view_factory.hpp: In function ‘typename boost::gil::color_converted_view_type<boost::gil::any_image_view<Types ...>, DstP>::type boost::gil::any_color_converted_view(const boost::gil::any_image_view<Types ...>&)’:  
../../boost/boost/gil/extension/dynamic_image/image_view_factory.hpp:402:71: warning: ‘typename boost::gil::color_converted_view_type<boost::gil::any_image_view<Types ...>, DstP>::type boost::gil::any_color_converted_view(const boost::gil::any_image_view<Types ...>&)’ is deprecated: Use color_converted_view(any_image_view<Views...> const& src) instead. [-Wdeprecated-declarations]  
  402 |     return apply_operation(src, detail::color_converted_view_fn<DstP, cc_view_t>());  
      |                                                                       ^~~~~~~~~  
../../boost/boost/gil/extension/dynamic_image/image_view_factory.hpp:398:6: note: declared here  
  398 | auto any_color_converted_view(const any_image_view<Views...>& src)  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
This warning is not emitted with clang 11. Currently I think it is a compiler bug in gcc 10.2.1, because we never invoke the deprecated function in the given example. Actually I already noticed this behavior while working on this PR and simply forgot to mention it as we had the discussion whether to merge this PR or #678. This PR does not emit the warning.  
  
I will try to complete #656 ASAP in order to solve this new problem, but also in order to get the apply_operation deprecation shipped with Boost 1.80.

---

## Comment 10

> Username: mloskot  
> Created_at: 2022-06-06 18:08:59 UTC  
> Updated_at: 2022-06-06 18:10:07 UTC  
> Url: https://github.com/boostorg/gil/pull/660#issuecomment-1147735559  

@marco-langer   
> Currently I think it is a compiler bug in gcc 10.2.1  
  
I think you may be correct: GCC **[Bug 95302](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=95302) - function attributed to be deprecated cannot include a typedef/using**  
It is marked as _"Known to fail: 10.2.0"_  
It looks like the `using cc_view_t =` needs to be unrolled in places of its use instead.  
  
Thank you!

---
