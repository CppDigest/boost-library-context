# #2 Cherry pick old changes from develop. [Closed]

> Username: danieljames  
> Created at: 2014-04-06 14:23:59 UTC  
> Updated at: 2014-07-13 17:52:59 UTC  
> Closed at: 2014-07-13 17:52:59 UTC  
> Url: https://github.com/boostorg/gil/pull/2  

Cherry picks some old changes that were never merged. This will leave the master equal to: ac89beceaa33c7007f9f9a15ffe49c84ae821f04.

---

## Comment 1

> Username: chhenning  
> Created_at: 2014-05-28 18:23:47 UTC  
> Url: https://github.com/boostorg/gil/pull/2#issuecomment-44445678  

The changes have been applied.  
  
On Sun, Apr 6, 2014 at 10:23 AM, Daniel James notifications@github.comwrote:  
  
> Cherry picks some old changes that were never merged. This will leave the  
> master equal to: ac89bechttps://github.com/boostorg/gil/commit/ac89beceaa33c7007f9f9a15ffe49c84ae821f04  
>   
> ## .  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/danieljames/gil cherry-pick  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/gil/pull/2  
> Commit Summary  
> - Added gil:: namespace to all at_c calls. This seems necessary for  
>   current gcc ( 4.4 and more ) compilers. See #3041 and #3619.  
> - GIL: Added support for copying between variants of different types  
> - Added test code to make sure cross byte pixel values work correctly.  
>   See comments.  
> - Disabled asserts since test code isn't working for big endian  
>   machines.  
>   
> File Changes  
> - _M_ include/boost/gil/color_base.hpphttps://github.com/boostorg/gil/pull/2/files#diff-0(46)  
> - _M_ include/boost/gil/extension/dynamic_image/any_image.hpphttps://github.com/boostorg/gil/pull/2/files#diff-1(6)  
> - _M_ include/boost/gil/extension/dynamic_image/any_image_view.hpphttps://github.com/boostorg/gil/pull/2/files#diff-2(6)  
> - _M_ include/boost/gil/extension/dynamic_image/reduce.hpphttps://github.com/boostorg/gil/pull/2/files#diff-3(2)  
> - _M_ include/boost/gil/extension/dynamic_image/variant.hpphttps://github.com/boostorg/gil/pull/2/files#diff-4(20)  
> - _M_ include/boost/gil/gil_concept.hpphttps://github.com/boostorg/gil/pull/2/files#diff-5(6)  
> - _M_ include/boost/gil/packed_pixel.hpphttps://github.com/boostorg/gil/pull/2/files#diff-6(10)  
> - _M_ test/pixel_iterator.cpphttps://github.com/boostorg/gil/pull/2/files#diff-7(50)  
>   
> Patch Links:  
> - https://github.com/boostorg/gil/pull/2.patch  
> - https://github.com/boostorg/gil/pull/2.diff  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/2  
> .

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-05-28 20:23:30 UTC  
> Url: https://github.com/boostorg/gil/pull/2#issuecomment-44459476  

I think this went a bit wrong. It really was meant for master, all the changes in this branch were already in develop from several years ago, but hadn't been merged to master, which makes it tricky to set up a merge base. The idea was that once they were applied, could 'merge' ac89bec into master and it would be a no-op, setting up git for easier merges to master in the future. Looking at 42fcf6d471f1fa4ea2a36b3afe3ba23c855ca89c you've included the conflict markers from the merge which are going to cause compile errors.  
  
I'll see if I can create a pull request to clean this up, but if you're having trouble with git, you shouldn't be afraid to ask, it's very confusing at first.

---

## Comment 3

> Username: chhenning  
> Created_at: 2014-05-28 20:47:19 UTC  
> Url: https://github.com/boostorg/gil/pull/2#issuecomment-44462256  

Daniel, I'm very sorry I messed up this change. I'll be more careful next  
time.  
  
Let me know if there is anything I can help.  
  
On Wed, May 28, 2014 at 4:23 PM, Daniel James notifications@github.comwrote:  
  
> I think this went a bit wrong. It really was meant for master, all the  
> changes in this branch were already in develop from several years ago, but  
> hadn't been merged to master, which makes it tricky to set up a merge base.  
> The idea was that once they were applied, could 'merge' ac89bechttps://github.com/boostorg/gil/commit/ac89becinto master and it would be a no-op, setting up git for easier merges to  
> master in the future. Looking at 42fcf6dhttps://github.com/boostorg/gil/commit/42fcf6d471f1fa4ea2a36b3afe3ba23c855ca89cyou've included the conflict markers from the merge which are going to  
> cause compile errors.  
>   
> I'll see if I can create a pull request to clean this up, but if you're  
> having trouble with git, you shouldn't be afraid to ask, it's very  
> confusing at first.  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/2#issuecomment-44459476  
> .

---

## Comment 4

> Username: danieljames  
> Created_at: 2014-05-28 20:56:54 UTC  
> Updated_at: 2014-05-28 20:57:16 UTC  
> Url: https://github.com/boostorg/gil/pull/2#issuecomment-44463377  

That's okay, git is pretty difficult to use, and with boost's complicated conversion from subversion it's extra confusing. I probably should have done a better job of describing what this pull request does. I tried creating a new pull request (#7) to clean this up.

---

## Comment 5

> Username: danieljames  
> Created_at: 2014-07-13 17:52:59 UTC  
> Url: https://github.com/boostorg/gil/pull/2#issuecomment-48847308  

Closing this as the other pull request was applied.

---
