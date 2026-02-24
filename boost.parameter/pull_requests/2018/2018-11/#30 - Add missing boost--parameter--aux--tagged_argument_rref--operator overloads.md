# #30  Add missing boost::parameter::aux::tagged_argument_rref::operator[] overloads [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-16 08:15:55 UTC  
> Updated at: 2018-11-17 03:29:35 UTC  
> Merged at: 2018-11-16 10:10:02 UTC  
> Closed at: 2018-11-16 10:10:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/30  

I'm still in the process of fixing compiler errors that are showing up in the BGL test suite.  I've tracked a couple of them down to "libs/graph/test/isomorphism.hpp", line 532, which reads, in part:  
  
arg_pack[_vertex_max_invariant | (invariant2.max)()]  
  
For compilers that support perfect forwarding, arg_pack is an object of type boost::parameter::aux::tagged_argument_rref<...>, but the [] operand evaluates to an object of type boost::parameter::aux::default_<...>, not boost::parameter::aux::default_r_<...>.  This PR aims to correct the deficiency by adding the missing overloads.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-11-16 10:09:51 UTC  
> Url: https://github.com/boostorg/parameter/pull/30#issuecomment-439345623  

Please also check the Parameter tests when perfect forwarding is not supported. My testing shows that some of the compile-fail cases do not fail when perfect forwarding is not supported. In particular evaluate_category_msvc_fail, preproc_eval_cat_msvc_fail. and compose_msvc11_fail appear to compile in those cases. This can be another issue, but please take a look at those situations.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-11-16 10:33:06 UTC  
> Url: https://github.com/boostorg/parameter/pull/30#issuecomment-439351952  

What I am seeing in the latest test of msvc-14.1, where perfect forwarding is supported, is that compose_msvc11_fail does not fail.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-11-16 12:19:40 UTC  
> Url: https://github.com/boostorg/parameter/pull/30#issuecomment-439376735  

I have changed the jamfile to accomodate msvc and toolsets for particular tests.

---

## Comment 4

> Username: CromwellEnage  
> Created_at: 2018-11-16 14:41:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/30#issuecomment-439413537  

Okay, thanks for that!

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-11-17 03:15:07 UTC  
> Url: https://github.com/boostorg/parameter/pull/30#issuecomment-439583395  

Please check the Issues for things I want to communicate to you regarding your work with Parameter.

---

## Comment 6

> Username: CromwellEnage  
> Created_at: 2018-11-17 03:29:35 UTC  
> Url: https://github.com/boostorg/parameter/pull/30#issuecomment-439584215  

I’m looking at them now.  
  
On Fri, Nov 16, 2018 at 22:15 Edward Diener <notifications@github.com>  
wrote:  
  
> Please check the Issues for things I want to communicate to you regarding  
> your work with Parameter.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/parameter/pull/30#issuecomment-439583395>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsOwQr0-cEHsCgcy2bBDUbYBXrqS6ks5uv388gaJpZM4Yltn3>  
> .  
>

---
