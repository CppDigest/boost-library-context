# #371 [DRAFT] Fix windows+mingw [Closed]

> Username: alandefreitas  
> Created at: 2022-08-08 16:14:17 UTC  
> Updated at: 2022-09-19 21:34:45 UTC  
> Closed at: 2022-09-15 19:49:51 UTC  
> Url: https://github.com/boostorg/url/pull/371  



---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-08-08 21:30:03 UTC  
> Updated_at: 2022-08-08 21:30:19 UTC  
> Url: https://github.com/boostorg/url/pull/371#issuecomment-1208632180  

I exported whatever needed to be exported. Now it wants `boost::core::basic_string_view<char>` to be exported. :neutral_face:   
  
```  
.\boost/url/params_encoded_view.hpp(83): error C2220: the following warning is treated as an error  
.\boost/url/params_encoded_view.hpp(83): warning C4251: 'boost::urls::params_encoded_view::s_': class 'boost::core::basic_string_view<char>' needs to have dll-interface to be used by clients of class 'boost::urls::params_encoded_view'  
```

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-08-08 22:11:47 UTC  
> Url: https://github.com/boostorg/url/pull/371#issuecomment-1208668335  

All solutions here are _very_ fragile. I don't know how to improve that. I'm commenting out MinGW in #370.

---
