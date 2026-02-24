# #22 - msm/back/state_machine, new_state_helper boost::placeholders namespace missing at bind [Closed]

> Username: BuzaL  
> Created at: 2019-06-17 19:28:04 UTC  
> Updated at: 2024-02-08 15:35:15 UTC  
> Closed at: 2024-02-08 15:35:15 UTC  
> Url: https://github.com/boostorg/msm/issues/22  

https://github.com/boostorg/msm/blob/009e6ac8d57241956781296d713321a0868d00e0/include/boost/msm/back/state_machine.hpp#L2387  
It could be a compile issue when using BOOST_BIND_NO_PLACEHOLDERS macro.

---

## Comment 1

> Username: BuzaL  
> Created at: 2019-06-17 19:38:55 UTC  
> Updated at: 2019-06-17 19:39:45 UTC  
> Url: https://github.com/boostorg/msm/issues/22#issuecomment-502821983  

Similare issue can be found at MSM_VISITOR_ARGS_EXECUTE macro:  
https://github.com/boostorg/msm/blob/009e6ac8d57241956781296d713321a0868d00e0/include/boost/msm/back/state_machine.hpp#L2308-L2330

---

## Comment 2

> Username: BuzaL  
> Created at: 2019-06-17 19:56:39 UTC  
> Url: https://github.com/boostorg/msm/issues/22#issuecomment-502827816  

my suggestion is, to insert using namespace after 2316 and 2324:  
`		    using namespace ::boost::placeholders;                                          \`  
Of course, where the SM will be included, should be include placeholders first.

---

## Comment 3

> Username: rogerorr  
> Created at: 2020-04-30 12:56:31 UTC  
> Url: https://github.com/boostorg/msm/issues/22#issuecomment-621816976  

With 1.73.0 you get a deprecation warning, which makes the problem noisier:  
-- msm_declaration.cpp --  
` #include <boost/msm/back/state_machine.hpp>`  
  
```  
cl /nologo /EHsc /I..\boost msm_deprecation.cpp /c  
msm_deprecation.cpp  
..\boost\boost/bind.hpp(41): note: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
```
