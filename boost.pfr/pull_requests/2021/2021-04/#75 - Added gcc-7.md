# #75 Added gcc-7 [Closed]

> Username: denzor200  
> Created at: 2021-04-02 21:12:01 UTC  
> Updated at: 2023-01-04 14:01:39 UTC  
> Closed at: 2023-01-04 14:01:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/75  



---

## Review 1 [Commented]

> Username: apolukhin  
> Created_at: 2021-04-03 18:57:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/75#pullrequestreview-627479219  

📁 include/boost/pfr/detail/integer_sequence.hpp

```diff
  14 |+ #endif
  15 |+ 
  16 |+ namespace boost { namespace pfr { namespace detail {
```

> Username: apolukhin  
> Created_at: 2021-04-03 18:57:26 UTC  
> Updated_at: 2021-05-04 21:59:13 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#discussion_r606698140  

Always include &lt;utility> and check here for the __cpp_lib_integer_sequence feature macro. In that way there's less library specific macro and no creepy "do not include before &lt;utility>" warnings

> Username: denzor200  
> Created_at: 2021-05-03 22:38:39 UTC  
> Updated_at: 2021-05-04 21:59:13 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#discussion_r625410714  

Not actual


---

## Review 2 [Commented]

> Username: apolukhin  
> Created_at: 2021-04-03 19:01:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/75#pullrequestreview-627479468  

📁 include/boost/pfr/detail/make_integer_sequence.hpp

```diff
  29 |- using std::integer_sequence;
  30 |- 
  31 |- // Clang unable to use namespace qualified std::integer_sequence in __make_integer_seq.
```

> Username: apolukhin  
> Created_at: 2021-04-03 19:01:14 UTC  
> Updated_at: 2021-05-04 21:59:13 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#discussion_r606698578  

This comment has reasons :)  
Put `using std::integer_sequence;` up above and do not use qualified name in `__make_integer_seq`

> Username: denzor200  
> Created_at: 2021-05-03 22:38:46 UTC  
> Updated_at: 2021-05-04 21:59:13 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#discussion_r625410745  

Not actual


---

## Comment 3

> Username: coveralls  
> Created_at: 2021-05-02 20:33:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-830867660  

## Pull Request Test Coverage Report for [Build 804969193](https://coveralls.io/builds/39292470)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/39292470/badge)](https://coveralls.io/builds/39292470) |  
| :-- | --: |  
| Change from base [Build 804489526](https://coveralls.io/builds/39290287): |  0.0% |  
| Covered Lines: | 325 |  
| Relevant Lines: | 325 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 4

> Username: coveralls  
> Created_at: 2021-05-02 20:33:08 UTC  
> Updated_at: 2023-01-04 10:01:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-830867663  

## Pull Request Test Coverage Report for [Build 3836890985](https://coveralls.io/builds/55626715)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/55626715/badge)](https://coveralls.io/builds/55626715) |  
| :-- | --: |  
| Change from base [Build 2983245262](https://coveralls.io/builds/52183663): |  0.0% |  
| Covered Lines: | 337 |  
| Relevant Lines: | 337 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 5

> Username: denzor200  
> Created_at: 2021-05-03 22:45:14 UTC  
> Updated_at: 2021-05-04 22:00:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-831581391  

Needs more informative error message in core17_generated.hpp

---

## Comment 6

> Username: denzor200  
> Created_at: 2021-05-15 09:26:45 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-841629242  

@apolukhin i'm finished

---

## Review 7 [Changes requested]

> Username: apolukhin  
> Created_at: 2021-07-24 18:42:49 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/75#pullrequestreview-714253355  

📁 test/Jamfile.v2

```diff
  13 |+ local USE_FALLBACK_FOR_BROKEN_STRUCTURED_BINDINGS = <define>BOOST_PFR_USE_CPP17=0 ;
  14 |+ 
  15 |+ if ! [ os.environ BOOST_PFR_TEST_WITH_FALLBACK_FOR_BROKEN_STRUCTURED_BINDINGS ]
```

> Username: apolukhin  
> Created_at: 2021-07-24 18:42:45 UTC  
> Updated_at: 2021-07-24 18:42:49 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#discussion_r676031730  

This does not scales well to other compilers.  
  
See `compiler_supports_loophole` and `loophole_detection.cpp`. Implement the structured binding check in a `structured_binding_detection.cpp` file and use that check.

> Username: denzor200  
> Created_at: 2022-09-06 10:12:42 UTC  
> Updated_at: 2022-09-06 10:12:43 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#discussion_r963525153  

not actual


---

## Comment 8

> Username: denzor200  
> Created_at: 2022-09-06 10:12:27 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-1237948314  

```  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.5.0/../../../../include/c++/5.5.0/tuple:807:33: error: implicit instantiation of undefined template 'std::tuple_size<my_struct>'  
    : integral_constant<size_t, tuple_size<_Tp>::value> { };  
                                ^  
./boost/pfr/detail/core17_generated.hpp:57:9: note: in instantiation of template class 'std::tuple_size<const my_struct>' requested here  
  auto& [a,b,c] = val; // ====================> Boost.PFR: User-provided type is not a SimpleAggregate.  
```  
another day, troubles are the same

---

## Comment 9

> Username: denzor200  
> Created_at: 2022-09-06 10:13:37 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-1237949724  

> ```  
> /usr/bin/../lib/gcc/x86_64-linux-gnu/5.5.0/../../../../include/c++/5.5.0/tuple:807:33: error: implicit instantiation of undefined template 'std::tuple_size<my_struct>'  
>     : integral_constant<size_t, tuple_size<_Tp>::value> { };  
>                                 ^  
> ./boost/pfr/detail/core17_generated.hpp:57:9: note: in instantiation of template class 'std::tuple_size<const my_struct>' requested here  
>   auto& [a,b,c] = val; // ====================> Boost.PFR: User-provided type is not a SimpleAggregate.  
> ```  
>   
> another day, troubles are the same  
  
i suppose, i know how to fix it https://godbolt.org/z/hWzsojGe6

---

## Comment 10

> Username: denzor200  
> Created_at: 2023-01-04 14:01:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/75#issuecomment-1370965331  

Closed because it's not actual.  
Actual PR here: https://github.com/boostorg/pfr/pull/116

---
