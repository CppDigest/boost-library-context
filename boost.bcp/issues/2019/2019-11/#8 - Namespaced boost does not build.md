# #8 - Namespaced boost does not build [Closed]

> Username: steveire  
> Created at: 2019-11-15 16:52:15 UTC  
> Updated at: 2019-11-25 09:32:11 UTC  
> Closed at: 2019-11-25 09:32:10 UTC  
> Url: https://github.com/boostorg/bcp/issues/8  

Using this script:  
  
```  
  
tar -xjf boost_1_71_0.tar.bz2  
cd boost_1_71_0  
./bootstrap.sh   
./b2 tools/bcp/  
mkdir nsboost  
./dist/bin/bcp --namespace=nsboost boost libs tools status LICENSE_1_0.txt b2 bjam boost-build.jam boostcpp.jam ./nsboost  
cd nsboost  
./bootstrap.sh  
./b2   
```  
  
I get this:  
  
```  
.  
gcc.compile.c++ bin.v2/libs/python/build/gcc-7/release/python-2.7/threading-multi/visibility-hidden/object/inheritance.o  
In file included from ./boost/unordered/detail/set.hpp:6:0,  
                 from ./boost/unordered/unordered_set.hpp:20,  
                 from ./boost/unordered_set.hpp:17,  
                 from ./boost/graph/adjacency_list.hpp:21,  
                 from libs/python/src/object/inheritance.cpp:11:  
./boost/unordered/detail/implementation.hpp:1606:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 1, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1606:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 1, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1606:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 1, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1606:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 1, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1607:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 2, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1607:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 2, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1607:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 2, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1607:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 2, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1608:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 3, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1608:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 3, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1608:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 3, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1608:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 3, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1609:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 4, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1609:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 4, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1609:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 4, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1609:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 4, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1610:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 5, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1610:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 5, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1610:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 5, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1610:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 5, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1611:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 6, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1611:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 6, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1611:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 6, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1611:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 6, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1612:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 7, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1612:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 7, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1612:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 7, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1612:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 7, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1613:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 8, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1613:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 8, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1613:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 8, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1613:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 8, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1614:52: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 9, boost)  
                                                    ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1614:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 9, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1614:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 9, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1614:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 9, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1615:53: error: ‘boost’ has not been declared  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 10, boost)  
                                                     ^  
./boost/unordered/detail/implementation.hpp:1583:5: note: in definition of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
     ^~~~~~~~~~  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected unqualified-id before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1615:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 10, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:56: error: expected ‘)’ before ‘const’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                        ^  
./boost/unordered/detail/implementation.hpp:1615:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 10, boost)  
         ^  
./boost/unordered/detail/implementation.hpp:1583:63: error: expected initializer before ‘x’  
     namespace_::tuple<BOOST_PP_ENUM_PARAMS_Z(z, n, A)> const& x)               \  
                                                               ^  
./boost/unordered/detail/implementation.hpp:1615:9: note: in expansion of macro ‘BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE’  
         BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE(1, 10, boost)  
         ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/usr/include/python2.7" -c -o "bin.v2/libs/python/build/gcc-7/release/python-2.7/threading-multi/visibility-hidden/object/inheritance.o" "libs/python/src/object/inheritance.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-7/release/python-2.7/threading-multi/visibility-hidden/object/inheritance.o...  
...failed updating 1 target...  
  
```  
  
It appears that the `boost` inside the `BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE` macro should be `nsboost`.

---

## Comment 1

> Username: steveire  
> Created at: 2019-11-15 17:14:48 UTC  
> Url: https://github.com/boostorg/bcp/issues/8#issuecomment-554447612  

After adding   
  
```  
         "|"  
            "(BOOST_UNORDERED_CONSTRUCT_FROM_TUPLE[^\\)]*)boost(\\))"  
```  
  
to the namespace matcher in `copy_path.hpp`, I was able to build.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-25 09:32:10 UTC  
> Url: https://github.com/boostorg/bcp/issues/8#issuecomment-558069132  

Patch applied, thanks!
