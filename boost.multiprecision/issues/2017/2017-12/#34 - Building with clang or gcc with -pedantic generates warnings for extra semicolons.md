# #34 - Building with clang or gcc with -pedantic generates warnings for extra semicolons [Closed]

> Username: jeking3  
> Created at: 2017-12-05 21:40:42 UTC  
> Updated at: 2019-06-19 00:53:49 UTC  
> Closed at: 2017-12-25 19:46:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/34  

Was building Boost.Random with cxxflags="-pedantic" and this occurred:  
  
```  
In file included from ../../boost/multiprecision/detail/default_ops.hpp:10:0,  
                 from ../../boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from ../../boost/multiprecision/number.hpp:22,  
                 from ../../boost/multiprecision/traits/extract_exponent_type.hpp:9,  
                 from ../../boost/multiprecision/debug_adaptor.hpp:9,  
                 from test/multiprecision_int_test.cpp:23:  
../../boost/multiprecision/detail/number_base.hpp:1006:24: warning: ISO C++ does not support â€˜__int128â€™ for â€˜type nameâ€™ [-Wpedantic]  
 struct number_category<__int128> : public mpl::int_<number_kind_integer> {};  
                        ^~~~~~~~  
../../boost/multiprecision/detail/number_base.hpp:1008:33: warning: ISO C++ does not support â€˜__int128â€™ for â€˜type nameâ€™ [-Wpedantic]  
 struct number_category<unsigned __int128> : public mpl::int_<number_kind_integer> {};  
                                 ^~~~~~~~  
In file included from test/multiprecision_int_test.cpp:23:0:  
../../boost/multiprecision/debug_adaptor.hpp:256:26: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(add, "+=");  
                          ^  
../../boost/multiprecision/debug_adaptor.hpp:257:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(subtract, "-=");  
                               ^  
../../boost/multiprecision/debug_adaptor.hpp:258:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(multiply, "*=");  
                               ^  
../../boost/multiprecision/debug_adaptor.hpp:259:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(divide, "/=");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:297:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(floor, "floor");  
                               ^  
../../boost/multiprecision/debug_adaptor.hpp:298:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(ceil, "ceil");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:299:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(sqrt, "sqrt");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:300:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(logb, "logb");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:315:25: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(add, "+");  
                         ^  
../../boost/multiprecision/debug_adaptor.hpp:316:30: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(subtract, "-");  
                              ^  
../../boost/multiprecision/debug_adaptor.hpp:317:30: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(multiply, "*");  
                              ^  
../../boost/multiprecision/debug_adaptor.hpp:318:28: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(divide, "/");  
                            ^  
../../boost/multiprecision/debug_adaptor.hpp:319:51: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(multiply_add, "fused-multiply-add");  
                                                   ^  
../../boost/multiprecision/debug_adaptor.hpp:320:61: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(multiply_subtract, "fused-multiply-subtract");  
                                                             ^  
../../boost/multiprecision/debug_adaptor.hpp:321:51: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP4(multiply_add, "fused-multiply-add");  
                                                   ^  
../../boost/multiprecision/debug_adaptor.hpp:322:61: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP4(multiply_subtract, "fused-multiply-subtract");  
                                                             ^  
../../boost/multiprecision/debug_adaptor.hpp:324:39: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP1(increment, "increment");  
                                       ^  
../../boost/multiprecision/debug_adaptor.hpp:325:39: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP1(decrement, "decrement");  
                                       ^  
../../boost/multiprecision/debug_adaptor.hpp:333:30: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(modulus, "%=");  
                              ^  
../../boost/multiprecision/debug_adaptor.hpp:334:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(modulus, "%");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:335:33: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(bitwise_or, "|=");  
                                 ^  
../../boost/multiprecision/debug_adaptor.hpp:336:32: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(bitwise_or, "|");  
                                ^  
../../boost/multiprecision/debug_adaptor.hpp:337:34: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(bitwise_and, "&=");  
                                  ^  
../../boost/multiprecision/debug_adaptor.hpp:338:33: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(bitwise_and, "&");  
                                 ^  
../../boost/multiprecision/debug_adaptor.hpp:339:34: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(bitwise_xor, "^=");  
                                  ^  
../../boost/multiprecision/debug_adaptor.hpp:340:33: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(bitwise_xor, "^");  
                                 ^  
../../boost/multiprecision/debug_adaptor.hpp:341:45: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP4(qr, "quotient-and-remainder");  
                                             ^  
../../boost/multiprecision/debug_adaptor.hpp:342:32: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(complement, "~");  
                                ^  
../../boost/multiprecision/debug_adaptor.hpp:423:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(gcd, "gcd");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:424:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(lcm, "lcm");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:425:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP4(powm, "powm");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:433:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(abs, "abs");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:434:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(fabs, "fabs");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:442:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(trunc, "trunc");  
                               ^  
../../boost/multiprecision/debug_adaptor.hpp:443:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(round, "round");  
                               ^  
../../boost/multiprecision/debug_adaptor.hpp:444:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(exp, "exp");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:445:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(log, "log");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:446:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(log10, "log10");  
                               ^  
../../boost/multiprecision/debug_adaptor.hpp:447:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(sin, "sin");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:448:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(cos, "cos");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:449:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(tan, "tan");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:450:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(asin, "asin");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:451:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(acos, "acos");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:452:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(atan, "atan");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:453:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(sinh, "sinh");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:454:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(cosh, "cosh");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:455:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP2(tanh, "tanh");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:456:29: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(fmod, "fmod");  
                             ^  
../../boost/multiprecision/debug_adaptor.hpp:457:27: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(pow, "pow");  
                           ^  
../../boost/multiprecision/debug_adaptor.hpp:458:31: warning: extra â€˜;â€™ [-Wpedantic]  
 NON_MEMBER_OP3(atan2, "atan2");  
                               ^  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-12-26 17:09:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/34#issuecomment-353990711  

Thanks, fixed in https://github.com/boostorg/multiprecision/commit/017f652ce3a1763917a1e602b7ef74882c7d3e11

---

## Comment 2

> Username: o2nwr  
> Created at: 2019-06-19 00:53:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/34#issuecomment-503364115  

1 warning left:  
...\boost_1_70_0\boost\multiprecision\debug_adaptor.hpp|425|warning: extra ';' [-Wpedantic]|
