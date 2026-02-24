# #532 Don't use deprecated form of user-defined literal operator [Merged]

> Username: Thomas-Barbier-1A  
> Created at: 2025-03-17 11:16:46 UTC  
> Updated at: 2025-07-04 00:02:23 UTC  
> Merged at: 2025-07-03 20:55:35 UTC  
> Closed at: 2025-07-03 20:55:35 UTC  
> Url: https://github.com/boostorg/hana/pull/532  



---

## Comment 1

> Username: Thomas-Barbier-1A  
> Created_at: 2025-03-17 11:22:43 UTC  
> Url: https://github.com/boostorg/hana/pull/532#issuecomment-2729130086  

I just noticed that I should have targeted master branch instead of develop, I will fix it

---

## Comment 2

> Username: greg7mdp  
> Created_at: 2025-07-03 20:50:55 UTC  
> Updated_at: 2025-07-03 20:52:27 UTC  
> Url: https://github.com/boostorg/hana/pull/532#issuecomment-3033608175  

Please merge this fix for 1.89. We're getting a ton of warnings with clang 20.1.3.  
  
```  
[1086/1397] Building CXX object libraries/chain/CMakeFiles/eosio_chain.dir/webassembly/action.cpp.o  
In file included from /__w/spring/spring/libraries/chain/webassembly/action.cpp:1:  
In file included from /__w/spring/spring/libraries/chain/include/eosio/chain/webassembly/interface.hpp:7:  
In file included from /__w/spring/spring/libraries/boost/libs/hana/include/boost/hana/string.hpp:15:  
In file included from /__w/spring/spring/libraries/boost/libs/hana/include/boost/hana/bool.hpp:13:  
In file included from /__w/spring/spring/libraries/boost/libs/hana/include/boost/hana/fwd/bool.hpp:13:  
/__w/spring/spring/libraries/boost/libs/hana/include/boost/hana/fwd/integral_constant.hpp:171:35: warning: identifier '_c' preceded by whitespace in a literal operator declaration is deprecated [-Wdeprecated-literal-operator]  
  171 |         constexpr auto operator"" _c();  
      |                        ~~~~~~~~~~~^~  
      |                        operator""_c  
```

---

## Comment 3

> Username: greg7mdp  
> Created_at: 2025-07-04 00:02:23 UTC  
> Url: https://github.com/boostorg/hana/pull/532#issuecomment-3033973066  

Thanks @ldionne , that was fast!

---
