# #822 Modernize exception specification [Merged]

> Username: apolukhin  
> Created at: 2022-09-17 16:06:45 UTC  
> Updated at: 2022-09-30 11:04:36 UTC  
> Merged at: 2022-09-27 18:07:58 UTC  
> Closed at: 2022-09-27 18:07:58 UTC  
> Url: https://github.com/boostorg/math/pull/822  

Done via `find include src/ example/ meta/ test/ tools/ include_private/ config/ -type f | xargs sed -i 's/throw()/BOOST_MATH_NOTHROW/g'` and by adding the include/boost/math/tools/nothrow.hpp

---

## Comment 1

> Username: mborland  
> Created_at: 2022-09-27 18:07:14 UTC  
> Url: https://github.com/boostorg/math/pull/822#issuecomment-1259871677  

Looks good to me. Sorry for the delay.

---
