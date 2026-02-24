# #19 fix syntax with BOOST_NO_EXCEPTIONS [Closed]

> Username: idupree  
> Created at: 2015-11-16 23:02:08 UTC  
> Updated at: 2016-12-21 08:35:11 UTC  
> Closed at: 2016-12-21 08:35:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/19  

There's already an ifdef around the catch block, so I added one around "try" to match.

---

## Comment 1

> Username: vprus  
> Created_at: 2016-12-21 08:35:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/19#issuecomment-268466131  

Cherry-picked to develop, thanks!

---
