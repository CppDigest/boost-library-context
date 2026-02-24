# #7 Clean up faulty merge in 42fcf6d. [Merged]

> Username: danieljames  
> Created at: 2014-05-28 20:33:23 UTC  
> Updated at: 2014-06-13 01:27:21 UTC  
> Merged at: 2014-05-28 21:24:13 UTC  
> Closed at: 2014-05-28 21:24:13 UTC  
> Url: https://github.com/boostorg/gil/pull/7  

This deletes a few files because they were previously deleted in develop, in 243fa33, but the merge restored them from master.

---

## Comment 1

> Username: chhenning  
> Created_at: 2014-05-28 20:59:17 UTC  
> Url: https://github.com/boostorg/gil/pull/7#issuecomment-44463674  

when I try this request on my develop branch, git tell me that "nothing to  
commit, working directory clean". Is that correct?  
  
On Wed, May 28, 2014 at 4:33 PM, Daniel James notifications@github.comwrote:  
  
> This deletes a few files because they were previously deleted in develop,  
> in 243fa33 https://github.com/boostorg/gil/commit/243fa33, but the  
>   
> ## merge restored them from master.  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/danieljames/gil cleanup  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/gil/pull/7  
> Commit Summary  
> - Clean up faulty merge in 42fcf6d.  
>   
> File Changes  
> - _M_ include/boost/gil/algorithm.hpphttps://github.com/boostorg/gil/pull/7/files#diff-0(9)  
> - _M_ include/boost/gil/channel.hpphttps://github.com/boostorg/gil/pull/7/files#diff-1(15)  
> - _M_ include/boost/gil/extension/dynamic_image/any_image.hpphttps://github.com/boostorg/gil/pull/7/files#diff-2(4)  
> - _M_ include/boost/gil/extension/dynamic_image/any_image_view.hpphttps://github.com/boostorg/gil/pull/7/files#diff-3(4)  
> - _M_  
>   include/boost/gil/extension/dynamic_image/apply_operation_base.hpphttps://github.com/boostorg/gil/pull/7/files#diff-4(8)  
> - _M_ include/boost/gil/extension/dynamic_image/variant.hpphttps://github.com/boostorg/gil/pull/7/files#diff-5(15)  
> - _D_ include/boost/gil/extension/io/png_dynamic_io.hpphttps://github.com/boostorg/gil/pull/7/files#diff-6(141)  
> - _D_ include/boost/gil/extension/io/png_io_private.hpphttps://github.com/boostorg/gil/pull/7/files#diff-7(360)  
> - _D_ include/boost/gil/extension/io/tiff_io.hpphttps://github.com/boostorg/gil/pull/7/files#diff-8(511)  
> - _M_ include/boost/gil/image.hpphttps://github.com/boostorg/gil/pull/7/files#diff-9(22)  
> - _M_ include/boost/gil/packed_pixel.hpphttps://github.com/boostorg/gil/pull/7/files#diff-10(4)  
> - _M_ include/boost/gil/planar_pixel_iterator.hpphttps://github.com/boostorg/gil/pull/7/files#diff-11(9)  
> - _M_ test/pixel_iterator.cpphttps://github.com/boostorg/gil/pull/7/files#diff-12(19)  
>   
> Patch Links:  
> - https://github.com/boostorg/gil/pull/7.patch  
> - https://github.com/boostorg/gil/pull/7.diff  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/7  
> .

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-05-28 21:03:29 UTC  
> Url: https://github.com/boostorg/gil/pull/7#issuecomment-44464178  

How did you try to merge it? Perhaps try running 'git log' to see if it has been merged.

---

## Comment 3

> Username: chhenning  
> Created_at: 2014-05-28 21:24:55 UTC  
> Url: https://github.com/boostorg/gil/pull/7#issuecomment-44466556  

I just pushed your change. Could you please double check.  
  
Thanks!  
  
On Wed, May 28, 2014 at 5:03 PM, Daniel James notifications@github.comwrote:  
  
> How did you try to merge it? Perhaps try running 'git log' to see if it  
> has been merged.  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/7#issuecomment-44464178  
> .

---

## Comment 4

> Username: danieljames  
> Created_at: 2014-05-28 21:32:42 UTC  
> Url: https://github.com/boostorg/gil/pull/7#issuecomment-44467387  

That looks good.

---
