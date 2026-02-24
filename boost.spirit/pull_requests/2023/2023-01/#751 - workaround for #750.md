# #751 workaround for #750 [Merged]

> Username: zhuoqiang  
> Created at: 2023-01-14 04:01:48 UTC  
> Updated at: 2025-05-09 09:51:57 UTC  
> Merged at: 2025-05-09 09:51:56 UTC  
> Closed at: 2025-05-09 09:51:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/751  

workaroud #750 by replacing `std::endl` with `'\n'`

---

## Comment 1

> Username: saki7  
> Created_at: 2025-05-09 09:51:21 UTC  
> Updated_at: 2025-05-09 09:51:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/751#issuecomment-2865912087  

I'm pretty much sure that the original issue (#750) has nothing to do with newlines being `std::endl` or `'\n`.  
  
It's quite unrealistic that the `wrong number of template arguments` error on `variant` is actually being affected by above code. I wonder if a transitive includes or some other environment-specific issue is affecting your code.   
  
That said, using `\n` instead of `std::endl` is indeed the widely accepted practice so I perhaps should merge the pr for a good reason.  
  
Thank you for your contribution!

---
