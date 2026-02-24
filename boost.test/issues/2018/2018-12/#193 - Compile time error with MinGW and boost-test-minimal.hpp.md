# #193 - Compile time error with MinGW and boost/test/minimal.hpp [Closed]

> Username: apolukhin  
> Created at: 2018-12-16 10:17:45 UTC  
> Updated at: 2020-04-06 12:25:54 UTC  
> Closed at: 2020-04-06 12:25:49 UTC  
> Url: https://github.com/boostorg/test/issues/193  

Attempt to build Boost.Variant tests with MinGW end with the following error:   
```  
In file included from ..\..\../boost/test/minimal.hpp:53:0,  
                 from variant_multivisit_test.cpp:18:  
..\..\../boost/test/impl/execution_monitor.ipp: In function 'void boost::detail::report_error(boost::execution_exception::error_code, const boost::exception*, const char*, char**)':  
..\..\../boost/test/impl/execution_monitor.ipp:221:84: error: 'vsnprintf' was not declared in this scope  
 #  define BOOST_TEST_VSNPRINTF( a1, a2, a3, a4 ) vsnprintf( (a1), (a2), (a3), (a4) )  
                                                                                    ^  
..\..\../boost/test/impl/execution_monitor.ipp:246:5: note: in expansion of macro 'BOOST_TEST_VSNPRINTF'  
     BOOST_TEST_VSNPRINTF( buf, sizeof(buf)-1, format, *args );  
     ^  
```  
Full log available at https://ci.appveyor.com/project/apolukhin/variant/build/job/hq2ob6ipas8g4qrc

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-03 19:11:04 UTC  
> Url: https://github.com/boostorg/test/issues/193#issuecomment-460079115  

Do you think it is solved through https://github.com/boostorg/test/pull/195 ? The changes are in the `next-internal` branch if you want to give a try.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2020-04-06 12:25:49 UTC  
> Url: https://github.com/boostorg/test/issues/193#issuecomment-609762527  

Closing. Feel free to re-open if you encounter the issue again.
