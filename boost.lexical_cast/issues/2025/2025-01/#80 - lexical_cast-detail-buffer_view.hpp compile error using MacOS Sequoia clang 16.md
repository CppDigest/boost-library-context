# #80 - lexical_cast/detail/buffer_view.hpp compile error using MacOS Sequoia clang 16 [Closed]

> Username: stefanottili  
> Created at: 2025-01-20 19:27:19 UTC  
> Updated at: 2025-01-25 00:20:13 UTC  
> Closed at: 2025-01-25 00:20:11 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/80  

The OpenROAD project relies on lemon-graph, which seems to have an issue with clang 16 on MacOS Sequoia.  
https://github.com/The-OpenROAD-Project/lemon-graph/issues/1  
```  
git clone https://github.com/The-OpenROAD-Project/lemon-graph.git  
cd lemon-graph  
mkdir build  
cd build   
cmake ..  
make  
```  
```  
...  
In file included from /Users/user/lemon-graph/lemon/soplex.cc:22:  
In file included from /opt/homebrew/include/soplex.h:35:  
In file included from /opt/homebrew/include/soplex/spxdefines.h:63:  
In file included from /opt/homebrew/include/boost/multiprecision/number.hpp:11:  
In file included from /opt/homebrew/include/boost/multiprecision/detail/precision.hpp:9:  
In file included from /opt/homebrew/include/boost/multiprecision/traits/is_variable_precision.hpp:9:  
In file included from /opt/homebrew/include/boost/multiprecision/detail/number_base.hpp:29:  
In file included from /opt/homebrew/include/boost/lexical_cast.hpp:30:  
/opt/homebrew/include/boost/lexical_cast/detail/buffer_view.hpp:27:34: error: expected '(' for function-style cast or type construction  
   27 |         return buffer_view<CharT>{begin, end};  
      |                ~~~~~~~~~~~~~~~~~~^  
/opt/homebrew/include/boost/lexical_cast/detail/buffer_view.hpp:31:33: error: expected '(' for function-style cast or type construction  
   31 |         return buffer_view<char>{  
      |                ~~~~~~~~~~~~~~~~~^  
/opt/homebrew/include/boost/lexical_cast/detail/buffer_view.hpp:38:33: error: expected '(' for function-style cast or type construction  
   38 |         return buffer_view<char>{  
      |                ~~~~~~~~~~~~~~~~~^  
fatal error: too many errors emitted, stopping now [-ferror-limit=]  
91 warnings and 20 errors generated.  
make[2]: *** [lemon/CMakeFiles/lemon.dir/soplex.cc.o] Error 1  
make[1]: *** [lemon/CMakeFiles/lemon.dir/all] Error 2  
```

---

## Comment 1

> Username: stefanottili  
> Created at: 2025-01-25 00:20:11 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/80#issuecomment-2613643605  

Matt Liberty knew that lemon-graph can/should be built without soplex  
'cmake -DLEMON_ENABLE_SOPLEX=FALSE -B build .'
