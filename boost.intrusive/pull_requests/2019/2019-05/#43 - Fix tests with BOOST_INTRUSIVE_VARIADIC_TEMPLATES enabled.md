# #43 Fix tests with BOOST_INTRUSIVE_VARIADIC_TEMPLATES enabled [Closed]

> Username: je4d  
> Created at: 2019-05-08 16:10:14 UTC  
> Updated at: 2019-05-10 21:52:36 UTC  
> Closed at: 2019-05-10 21:52:36 UTC  
> Url: https://github.com/boostorg/intrusive/pull/43  

Allow void as an option with `BOOST_INTRUSIVE_VARIADIC_TEMPLATES` enabled - this matches the behaviour with it disabled. The tests have recently started to depend on being able to pass void as an option with no effect.  
  
@igaztanaga, why is `BOOST_INTRUSIVE_VARIADIC_TEMPLATES` disabled by default? Looking at the history, it was enabled way back in 2011 and was removed in a seemingly unrelated change (8a53a5af)

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-05-10 21:52:36 UTC  
> Url: https://github.com/boostorg/intrusive/pull/43#issuecomment-491440832  

Many thanks for the patch.  
  
BOOST_INTRUSIVE_VARIADIC_TEMPLATES was disabled by error. I've fixed this in commit https://github.com/boostorg/intrusive/commit/2af47857f49ae08eb5478d3abd77dd33c28b381e

---
