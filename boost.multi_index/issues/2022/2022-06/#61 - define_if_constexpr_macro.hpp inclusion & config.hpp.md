# #61 - define_if_constexpr_macro.hpp inclusion & config.hpp [Closed]

> Username: urnathan  
> Created at: 2022-06-30 20:00:26 UTC  
> Updated at: 2022-07-01 08:00:45 UTC  
> Closed at: 2022-07-01 08:00:02 UTC  
> Url: https://github.com/boostorg/multi_index/issues/61  

It seems that boost/multi_index/detail/define_if_constexpr_macro.hpp is included inside inline member functions (multi_index_container.hpp around line 370 for instance).   define_if_constexpr_macro.hpp #includes <boost/config.h>.  That's problematic.  
  
It causes problems with clang implicit modules, if one tries to make boost/config.h (/and/ its entire subgraph) importable headers (and by implication C++20 header units).  
  
But AFAICT, it's just plain wrong, because boost/config.hpp can expand to namespace-scope code on first inclusion -- config/detail/suffix.hpp contains typedefs for a 64bit int type and templates for missing min/max fns.  So if define_if_constexpr_macro.hpp contains the first inclusion, things will go sadly wrong anyway.  (I don't think this happens, because the users of define_if_constexpr_macro have already included config.hpp.  
  
Either remove the #include <boost/config.hpp> from define_if_constexpr_macro.hpp or replace it with something like:  
```  
-#include <boost/config.hpp>  
+#ifndef BOOST_CONFIG_HPP  
+// We can't #include config.hpp ourselves as it can expand to  
+// top-level source, and this file is intended for inclusion inside  
+// functions (for instance).  
+#error "#include <boost/config.hpp> missing"  
+#endif  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2022-07-01 08:00:45 UTC  
> Url: https://github.com/boostorg/multi_index/issues/61#issuecomment-1172056168  

Thanks for spotting this problem! Fixed in https://github.com/boostorg/multi_index/commit/99f33def00c5e6e7013e297a71d3c422698438c6.
