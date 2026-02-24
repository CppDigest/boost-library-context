# #16 Fix for MSVC compile break [Closed]

> Username: MarcelRaad  
> Created at: 2015-05-09 14:02:11 UTC  
> Updated at: 2015-05-10 14:31:41 UTC  
> Closed at: 2015-05-10 14:31:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/16  

When BOOST_ARCHIVE_SOURCE was defined and BOOST_WARCHIVE_SOURCE was not  
defined, BOOST_ARCHIVE_OR_WARCHIVE_DECL was erroneously defined as  
BOOST_SYMBOL_IMPORT instead of BOOST_SYMBOL_EXPORT.

---

## Comment 1

> Username: robertramey  
> Created_at: 2015-05-09 15:27:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/16#issuecomment-100506163  

Thanks for this.  
  
I was stuck.  I merged your changes in by hand, retested here and pushed to boost/serialzation repo.  
  
For other reasons, I’ve had to rehabilitate my XP laptop so in the future I should be able to run tests for MSVC 9.0  
  
again, thanks for helping me out with this.  
  
> On May 9, 2015, at 7:02 AM, Marcel Raad notifications@github.com wrote:  
>   
> When BOOST_ARCHIVE_SOURCE was defined and BOOST_WARCHIVE_SOURCE was not  
> defined, BOOST_ARCHIVE_OR_WARCHIVE_DECL was erroneously defined as  
> BOOST_SYMBOL_IMPORT instead of BOOST_SYMBOL_EXPORT.  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/serialization/pull/16 https://github.com/boostorg/serialization/pull/16  
> Commit Summary  
>   
> Fix for MSVC compile break  
> File Changes  
>   
> M include/boost/archive/detail/decl.hpp https://github.com/boostorg/serialization/pull/16/files#diff-0 (7)  
> Patch Links:  
>   
> https://github.com/boostorg/serialization/pull/16.patch https://github.com/boostorg/serialization/pull/16.patch  
> https://github.com/boostorg/serialization/pull/16.diff https://github.com/boostorg/serialization/pull/16.diff  
> —  
> Reply to this email directly or view it on GitHub https://github.com/boostorg/serialization/pull/16.  
  
Robert Ramey  
Robert Ramey Software Development  
(805)569-3793  
ramey@rrsd.com  
www.rrsd.com

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-05-09 22:03:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/16#issuecomment-100553400  

Thanks!  
Shouldn't the `#define BOOST_ARCHIVE_OR_WARCHIVE_DECL` be inside the `#if (defined(BOOST_ALL_DYN_LINK) || defined(BOOST_SERIALIZATION_DYN_LINK))` block?  
  
I just noticed that there's one compilation error left on MSVC: in shared_ptr_helper.hpp, there is a missing \ at the end of line 74.

---
