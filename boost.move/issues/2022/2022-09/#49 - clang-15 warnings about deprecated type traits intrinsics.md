# #49 - clang-15 warnings about deprecated type traits intrinsics [Closed]

> Username: Lastique  
> Created at: 2022-09-08 23:10:30 UTC  
> Updated at: 2022-10-19 19:41:46 UTC  
> Closed at: 2022-10-19 19:41:46 UTC  
> Url: https://github.com/boostorg/move/issues/49  

clang-15 [generates](https://github.com/boostorg/filesystem/runs/8260115513?check_suite_focus=true#step:7:500) the following warnings:  
  
```  
In file included from libs/filesystem/test/path_unit_test.cpp:37:  
In file included from ./boost/smart_ptr.hpp:24:  
In file included from ./boost/make_shared.hpp:14:  
In file included from ./boost/smart_ptr/make_shared.hpp:14:  
In file included from ./boost/smart_ptr/make_shared_object.hpp:15:  
In file included from ./boost/move/core.hpp:58:  
./boost/move/detail/type_traits.hpp:1003:29: warning: builtin __has_trivial_move_constructor is deprecated; use __is_trivially_constructible instead [-Wdeprecated-builtins]  
{ static const bool value = BOOST_MOVE_IS_TRIVIALLY_MOVE_CONSTRUCTIBLE(T); };  
                            ^  
./boost/move/detail/type_traits.hpp:418:60: note: expanded from macro 'BOOST_MOVE_IS_TRIVIALLY_MOVE_CONSTRUCTIBLE'  
   #define BOOST_MOVE_IS_TRIVIALLY_MOVE_CONSTRUCTIBLE(T)   BOOST_MOVE_HAS_TRIVIAL_MOVE_CONSTRUCTOR(T) || ::boost::move_detail::is_pod<T>::value  
                                                           ^  
./boost/move/detail/type_traits.hpp:196:57: note: expanded from macro 'BOOST_MOVE_HAS_TRIVIAL_MOVE_CONSTRUCTOR'  
#     define BOOST_MOVE_HAS_TRIVIAL_MOVE_CONSTRUCTOR(T) __has_trivial_move_constructor(T)  
                                                        ^  
./boost/move/detail/type_traits.hpp:1019:30: warning: builtin __has_trivial_move_assign is deprecated; use __is_trivially_assignable instead [-Wdeprecated-builtins]  
{  static const bool value = BOOST_MOVE_IS_TRIVIALLY_MOVE_ASSIGNABLE(T);  };  
                             ^  
./boost/move/detail/type_traits.hpp:432:56: note: expanded from macro 'BOOST_MOVE_IS_TRIVIALLY_MOVE_ASSIGNABLE'  
   #define BOOST_MOVE_IS_TRIVIALLY_MOVE_ASSIGNABLE(T)  BOOST_MOVE_HAS_TRIVIAL_MOVE_ASSIGN(T) || ::boost::move_detail::is_pod<T>::value  
                                                       ^  
./boost/move/detail/type_traits.hpp:203:52: note: expanded from macro 'BOOST_MOVE_HAS_TRIVIAL_MOVE_ASSIGN'  
#     define BOOST_MOVE_HAS_TRIVIAL_MOVE_ASSIGN(T) __has_trivial_move_assign(T)  
                                                   ^  
./boost/move/detail/type_traits.hpp:1040:30: warning: builtin __has_trivial_move_assign is deprecated; use __is_trivially_assignable instead [-Wdeprecated-builtins]  
{  static const bool value = BOOST_MOVE_IS_NOTHROW_MOVE_CONSTRUCTIBLE(T);  };  
                             ^  
./boost/move/detail/type_traits.hpp:464:58: note: expanded from macro 'BOOST_MOVE_IS_NOTHROW_MOVE_CONSTRUCTIBLE'  
   #define BOOST_MOVE_IS_NOTHROW_MOVE_CONSTRUCTIBLE(T)   BOOST_MOVE_IS_TRIVIALLY_MOVE_ASSIGNABLE(T)  
                                                         ^  
./boost/move/detail/type_traits.hpp:432:56: note: expanded from macro 'BOOST_MOVE_IS_TRIVIALLY_MOVE_ASSIGNABLE'  
   #define BOOST_MOVE_IS_TRIVIALLY_MOVE_ASSIGNABLE(T)  BOOST_MOVE_HAS_TRIVIAL_MOVE_ASSIGN(T) || ::boost::move_detail::is_pod<T>::value  
                                                       ^  
./boost/move/detail/type_traits.hpp:203:52: note: expanded from macro 'BOOST_MOVE_HAS_TRIVIAL_MOVE_ASSIGN'  
#     define BOOST_MOVE_HAS_TRIVIAL_MOVE_ASSIGN(T) __has_trivial_move_assign(T)  
                                                   ^  
./boost/move/detail/type_traits.hpp:1054:30: warning: builtin __has_nothrow_move_assign is deprecated; use __is_nothrow_assignable instead [-Wdeprecated-builtins]  
{  static const bool value = BOOST_MOVE_IS_NOTHROW_MOVE_ASSIGNABLE(T);  };  
                             ^  
./boost/move/detail/type_traits.hpp:468:53: note: expanded from macro 'BOOST_MOVE_IS_NOTHROW_MOVE_ASSIGNABLE'  
   #define BOOST_MOVE_IS_NOTHROW_MOVE_ASSIGNABLE(T) BOOST_MOVE_HAS_NOTHROW_MOVE_ASSIGN(T) || ::boost::move_detail::is_pod<T>::value  
                                                    ^  
./boost/move/detail/type_traits.hpp:217:52: note: expanded from macro 'BOOST_MOVE_HAS_NOTHROW_MOVE_ASSIGN'  
#     define BOOST_MOVE_HAS_NOTHROW_MOVE_ASSIGN(T) __has_nothrow_move_assign(T)  
                                                   ^  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-09-12 23:16:01 UTC  
> Url: https://github.com/boostorg/move/issues/49#issuecomment-1244697679  

See also https://github.com/boostorg/type_traits/pull/174.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-10-19 17:32:00 UTC  
> Url: https://github.com/boostorg/move/issues/49#issuecomment-1284351296  

I'm getting these as well from SmartPtr, which is a problem because I test with `warnings-as-errors=on`. Can we please get that fixed?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2022-10-19 19:00:30 UTC  
> Url: https://github.com/boostorg/move/issues/49#issuecomment-1284446437  

How can I reproduce it?  
  
https://godbolt.org/z/5c1EhhPPT

---

## Comment 4

> Username: igaztanaga  
> Created at: 2022-10-19 19:22:41 UTC  
> Url: https://github.com/boostorg/move/issues/49#issuecomment-1284469437  

Got it, we need to use -std=c++03.
