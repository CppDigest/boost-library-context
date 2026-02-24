# #27  Add metafunction boost::parameter::is_argument_pack [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-09 05:35:04 UTC  
> Updated at: 2018-11-12 13:16:09 UTC  
> Merged at: 2018-11-09 15:09:57 UTC  
> Closed at: 2018-11-09 15:09:57 UTC  
> Url: https://github.com/boostorg/parameter/pull/27  

Also upgrade boost::parameter::aux::arg_list, boost::parameter::aux::tagged_argument, and boost::parameter::aux::tagged_argument_rref to model the MPL Associative Sequence concept.

---

## Comment 1

> Username: CromwellEnage  
> Created_at: 2018-11-09 05:39:29 UTC  
> Url: https://github.com/boostorg/parameter/pull/27#issuecomment-437256237  

Boost.Convert currently makes use of boost::parameter::aux::tag, which is not part of the public interface.  With these proposed enhancements, I can subsequently submit a PR to Boost.Convert so that it uses the public interface instead.

---
