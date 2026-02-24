# #35 Add docs and test for BOOST_NO_CXX11_NON_PUBLIC_DEFAULTED_FUNCTIONS [Merged]

> Username: Lastique  
> Created at: 2014-08-21 13:33:58 UTC  
> Updated at: 2014-08-22 18:29:49 UTC  
> Merged at: 2014-08-22 17:53:29 UTC  
> Closed at: 2014-08-22 17:53:29 UTC  
> Url: https://github.com/boostorg/config/pull/35  

The pull request addresses comments to the commit https://github.com/boostorg/config/commit/411399ec60c0f56e77aa56da830945100a4b263e.  
- The macro is now defined when defaulted functions are not supported at all.  
- Docs and test added.  
- The macro is no longer defined for gcc 4.4 as it passes the test.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-22 17:55:00 UTC  
> Url: https://github.com/boostorg/config/pull/35#issuecomment-53097208  

Thanks for that.  I reverted and reworked one of the commits as it seemed to generate a lot of churn in the generated files that shouldn't have been there, but <shrug> I guess.

---
