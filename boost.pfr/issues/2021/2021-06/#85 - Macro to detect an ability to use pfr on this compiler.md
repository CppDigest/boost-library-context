# #85 - Macro to detect an ability to use pfr on this compiler [Closed]

> Username: denzor200  
> Created at: 2021-06-20 11:24:47 UTC  
> Updated at: 2025-06-20 14:47:16 UTC  
> Closed at: 2025-06-20 14:47:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/85  

This macro will help me embed boost.pfr into boost.fusion without breaking backward compatibility.  
Without this macro, I am forced to provide backward compatibility with a dirty hack like:  
https://github.com/denzor200/fusion/blob/e9d7fcdcd8de4e66bff5427726d777c222e7590d/include/boost/fusion/support/config.hpp#L136

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-06-20 14:47:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/85#issuecomment-2991910715  

The macro `BOOST_PFR_ENABLED` was implemented a few years ago
