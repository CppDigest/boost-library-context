# #479 - Ensure that all public headers are standalone [Open]

> Username: anarthal  
> Created at: 2025-05-07 09:33:59 UTC  
> Updated at: 2025-05-07 09:33:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/479  

Add tests to verify that we don't have missing includes in public headers, like Beast does. Should avoid issues like #475.
