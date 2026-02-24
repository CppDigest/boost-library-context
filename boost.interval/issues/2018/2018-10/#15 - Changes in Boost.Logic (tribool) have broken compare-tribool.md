# #15 - Changes in Boost.Logic (tribool) have broken compare/tribool [Open]

> Username: jeking3  
> Created at: 2018-10-30 11:30:37 UTC  
> Updated at: 2018-10-30 11:30:37 UTC  
> Url: https://github.com/boostorg/interval/issues/15  

```  
gcc.compile.c++ ../../../../bin.v2/libs/numeric/interval/test/cmp_tribool.test/gcc-7.3/debug/cxxstd-11-iso/visibility-hidden/libs/numeric/interval/test/cmp_tribool.o  
In file included from ../../../../boost/test/tools/old/impl.hpp:20:0,  
                 from ../../../../boost/test/test_tools.hpp:46,  
                 from /boost/libs/numeric/interval/test/cmp_header.hpp:14,  
                 from /boost/libs/numeric/interval/test/cmp_tribool.cpp:11:  
../../../../boost/test/tools/assertion_result.hpp: In instantiation of 'boost::test_tools::assertion_result::assertion_result(const BoolConvertable&) [with BoolConvertable = boost::logic::tribool]':  
/boost/libs/numeric/interval/test/cmp_tribool.cpp:21:3:   required from here  
../../../../boost/test/tools/assertion_result.hpp:57:79: error: no matching function for call to 'boost::test_tools::assertion_result::readonly_property65::readonly_property65(boost::logic::tribool)'  
     assertion_result( BoolConvertable const& pv_ ) : p_predicate_value( !!pv_ ) {}  
                                                                               ^  
In file included from ../../../../boost/config/detail/suffix.hpp:543:0,  
                 from ../../../../boost/config.hpp:61,  
                 from ../../../../boost/limits.hpp:14,  
                 from ../../../../boost/numeric/interval/checking.hpp:16,  
                 from /boost/libs/numeric/interval/test/cmp_header.hpp:11,  
                 from /boost/libs/numeric/interval/test/cmp_tribool.cpp:11:  
../../../../boost/test/utils/class_properties.hpp:155:29: note: candidate: boost::test_tools::assertion_result::readonly_property65::readonly_property65(write_param_t)  
     explicit    BOOST_JOIN( readonly_property, __LINE__ )( write_param_t init_v  )  \  
                             ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:155:29: note:   no known conversion for argument 1 from 'boost::logic::tribool' to 'write_param_t {aka bool}'  
     explicit    BOOST_JOIN( readonly_property, __LINE__ )( write_param_t init_v  )  \  
                             ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:154:29: note: candidate: boost::test_tools::assertion_result::readonly_property65::readonly_property65()  
                 BOOST_JOIN( readonly_property, __LINE__ )() {}                      \  
                             ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:154:29: note:   candidate expects 0 arguments, 1 provided  
                 BOOST_JOIN( readonly_property, __LINE__ )() {}                      \  
                             ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:148:19: note: candidate: constexpr boost::test_tools::assertion_result::readonly_property65::readonly_property65(const boost::test_tools::assertion_result::readonly_property65&)  
 class BOOST_JOIN( readonly_property, __LINE__ )                                     \  
                   ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:148:19: note:   no known conversion for argument 1 from 'boost::logic::tribool' to 'const boost::test_tools::assertion_result::readonly_property65&'  
 class BOOST_JOIN( readonly_property, __LINE__ )                                     \  
                   ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:148:19: note: candidate: constexpr boost::test_tools::assertion_result::readonly_property65::readonly_property65(boost::test_tools::assertion_result::readonly_property65&&)  
 class BOOST_JOIN( readonly_property, __LINE__ )                                     \  
                   ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
../../../../boost/test/utils/class_properties.hpp:148:19: note:   no known conversion for argument 1 from 'boost::logic::tribool' to 'boost::test_tools::assertion_result::readonly_property65&&'  
 class BOOST_JOIN( readonly_property, __LINE__ )                                     \  
                   ^  
../../../../boost/test/tools/assertion_result.hpp:65:5: note: in expansion of macro 'BOOST_READONLY_PROPERTY'  
     BOOST_READONLY_PROPERTY( bool, (assertion_result) ) p_predicate_value;  
     ^~~~~~~~~~~~~~~~~~~~~~~  
  
    "/usr/bin/g++-7"   -std=c++11 -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden -frounding-math -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1  -I"../../../.." -c -o "../../../../bin.v2/libs/numeric/interval/test/cmp_tribool.test/gcc-7.3/debug/cxxstd-11-iso/visibility-hidden/libs/numeric/interval/test/cmp_tribool.o" "/boost/libs/numeric/interval/test/cmp_tribool.cpp"  
  
...failed gcc.compile.c++ ../../../../bin.v2/libs/numeric/interval/test/cmp_tribool.test/gcc-7.3/debug/cxxstd-11-iso/visibility-hidden/libs/numeric/interval/test/cmp_tribool.o...  
```
