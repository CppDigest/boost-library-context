# #118 Modernize thow specifications [Closed]

> Username: apolukhin  
> Created at: 2022-09-09 11:14:07 UTC  
> Updated at: 2024-04-12 17:29:05 UTC  
> Closed at: 2024-04-12 17:29:05 UTC  
> Url: https://github.com/boostorg/program_options/pull/118  

Done via `find include/ src/ -type f | xargs sed -i 's/throw()/BOOST_NOEXCEPT_OR_NOTHROW/g'`

---

## Comment 1

> Username: apolukhin  
> Created_at: 2024-04-12 17:29:05 UTC  
> Url: https://github.com/boostorg/program_options/pull/118#issuecomment-2052183337  

Fixed in https://github.com/boostorg/program_options/pull/133

---
