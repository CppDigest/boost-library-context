# #18 - Remove deprecated Boost.Test code. [Closed]

> Username: brandon-kohn  
> Created at: 2019-04-18 16:57:22 UTC  
> Updated at: 2022-11-16 08:50:12 UTC  
> Closed at: 2022-11-16 08:50:11 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/18  

Boost.Test has deprecated the use of non-header only builds. This is a reminder to change numeric_conversion to use the header only version.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-16 08:50:11 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/18#issuecomment-1316618410  

I believe this is now obsolete because of https://github.com/boostorg/numeric_conversion/commit/db44689f4f4f74d6572a868e13f523c82fca5a55.
