# #115 - Boost 1.81.0: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'? [Open]

> Username: wally-mageia  
> Created at: 2022-12-25 08:08:39 UTC  
> Updated at: 2025-12-09 11:58:00 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115  

When trying to build [mapnik](https://github.com/mapnik/mapnik) 3.1.0 with boost 1.81.0 having a patch from https://github.com/boostorg/phoenix/pull/112 and gcc 12.2.1 in Mageia Cauldron the build fails with:  
```  
c++ -o plugins/input/csv/csv_utils.o -c -std=c++14 -DU_USING_ICU_NAMESPACE=0 -O2 -g -pipe -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fstack-protector --param=ssp-buffer-size=4 -fasynchronous-unwind-tables -fvisibility=hidden -fvisibility-inlines-hidden -Wall -pthread -ftemplate-depth-300 -Wsign-compare -Wshadow -O2 -DMAPNIK_MEMORY_MAPPED_FILE -DMAPNIK_HAS_DLCFN -DBIGINT -DBOOST_REGEX_HAS_ICU -DHAVE_JPEG -DHAVE_PNG -DHAVE_WEBP -DHAVE_TIFF -DLINUX -DMAPNIK_THREADSAFE -DBOOST_SPIRIT_NO_PREDEFINED_TERMINALS=1 -DBOOST_PHOENIX_NO_PREDEFINED_TERMINALS=1 -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DNDEBUG -Ideps -Ideps/mapbox/variant/include -Ideps/agg/include -Iinclude -I/usr/include -I/usr/include/freetype2 -I/usr/include/libpng16 -I/usr/include/harfbuzz -I/usr/include/glib-2.0 -I/usr/lib64/glib-2.0/include -I/usr/include/libxml2 plugins/input/csv/csv_utils.cpp  
In file included from /usr/include/boost/phoenix/stl.hpp:14,  
                 from /usr/include/boost/phoenix/phoenix.hpp:19,  
                 from include/mapnik/wkt/wkt_factory.hpp:26,  
                 from plugins/input/csv/csv_utils.cpp:27:  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:16:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   16 |         BOOST_PP_LOCAL_MACRO(1)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:19:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   19 |         BOOST_PP_LOCAL_MACRO(2)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:22:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   22 |         BOOST_PP_LOCAL_MACRO(3)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:25:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   25 |         BOOST_PP_LOCAL_MACRO(4)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:28:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   28 |         BOOST_PP_LOCAL_MACRO(5)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:31:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   31 |         BOOST_PP_LOCAL_MACRO(6)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:34:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   34 |         BOOST_PP_LOCAL_MACRO(7)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:37:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   37 |         BOOST_PP_LOCAL_MACRO(8)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:40:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   40 |         BOOST_PP_LOCAL_MACRO(9)  
      |         ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/preprocessor/iteration/detail/limits/local_256.hpp:43:9: error: 'arg1' is not a member of 'boost::phoenix::placeholders'; did you mean 'uarg1'?  
   43 |         BOOST_PP_LOCAL_MACRO(10)  
      |         ^~~~~~~~~~~~~~~~~~~~  
In file included from /usr/include/boost/geometry/core/config.hpp:16,  
                 from /usr/include/boost/geometry/strategy/cartesian/side_by_triangle.hpp:27,  
                 from /usr/include/boost/geometry/strategies/strategies.hpp:137,  
                 from /usr/include/boost/geometry/geometry.hpp:57,  
                 from /usr/include/boost/geometry.hpp:17,  
                 from include/mapnik/geometry_adapters.hpp:38,  
                 from include/mapnik/geometry_correct.hpp:27,  
                 from plugins/input/csv/csv_utils.cpp:26:  
/usr/include/boost/spirit/include/phoenix_function.hpp:12:1: note: '#pragma message: This header is deprecated. Use <boost/phoenix/function.hpp> instead.'  
   12 | BOOST_HEADER_DEPRECATED("<boost/phoenix/function.hpp>")  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: wally-mageia  
> Created at: 2022-12-25 08:10:19 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115#issuecomment-1364642684  

See also https://github.com/mapnik/mapnik/issues/4375.

---

## Comment 2

> Username: wally-mageia  
> Created at: 2022-12-25 08:18:52 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115#issuecomment-1364643523  

Build continues with a workaround from https://github.com/boostorg/phoenix/issues/111#issuecomment-1355315671: `-DBOOST_PHOENIX_STL_TUPLE_H_`.

---

## Comment 3

> Username: tomhughes  
> Created at: 2023-02-26 22:16:18 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115#issuecomment-1445482360  

The problem is that `stl/tuple.hpp` assumes that `boost::phoenix::placeholders::argN` is defined, and uses it to define `uargN`, but if `BOOST_PHOENIX_NO_PREDEFINED_TERMINALS` is set then that is not true.

---

## Comment 4

> Username: djowel  
> Created at: 2023-02-27 00:03:21 UTC  
> Updated at: 2023-02-27 00:08:08 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115#issuecomment-1445508222  

I think we need to fully revert this nagging issue. Any takers?

---

## Comment 5

> Username: LowLevelMahn  
> Created at: 2023-12-14 07:56:00 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115#issuecomment-1855351334  

fixed with 1.84.0

---

## Comment 6

> Username: jwakely  
> Created at: 2025-12-09 11:58:00 UTC  
> Url: https://github.com/boostorg/phoenix/issues/115#issuecomment-3631879596  

> fixed with 1.84.0  
  
Addressed by 8913607a3788cb82d48ed461ea59c919b7bad3df although it has "FIX ME" so I guess that's why this issue is still open.
