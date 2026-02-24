# #78 - "static_assert_unrecognized_pretty_function_format_please_file_github_issue" on clang-cl 19.1.0, boost 1.87 [Closed]

> Username: Mishura4  
> Created at: 2025-01-27 17:08:41 UTC  
> Updated at: 2025-02-23 07:27:11 UTC  
> Closed at: 2025-02-23 07:27:11 UTC  
> Url: https://github.com/boostorg/leaf/issues/78  

```   
  In file included from <proprietary code>  
  In file included from D:\Dev\Install\include\boost-1_87\boost\cobalt.hpp:20:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\cobalt\leaf.hpp:8:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\cobalt\detail\leaf.hpp:10:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\leaf\handle_errors.hpp:9:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\leaf\context.hpp:9:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp:11:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\leaf\detail\capture_list.hpp:9:  
  In file included from D:\Dev\Install\include\boost-1_87\boost\leaf\detail\print.hpp:9:  
D:\Dev\Install\include\boost-1_87\boost\leaf\detail\demangle.hpp(179,89): error : array is too large (18446744073709551615 elements)  
    179 |         char static_assert_unrecognized_pretty_function_format_please_file_github_issue[sizeof(  
        |                                                                                         ^~~~~~~  
    180 |             char[  
        |             ~~~~~  
    181 |                 (s01 && (1 == (!!p01 + !!p02 + !!p03)))  
        |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    182 |                 ||  
        |                 ~~  
    183 |                 (s02 && (1 == (!!p04 + !!p05 + !!p06 + !!p07 + !!p08 + !!p09 + !!p10 + !!p11 + !!p12)))  
        |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    184 |                 ||  
        |                 ~~  
    185 |                 (s02 && (1 == (!!p13 + !!p14 + !!p15)))  
        |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    186 |             ]  
        |             ~  
    187 |         ) * 2 - 1];  
        |         ~~~~~~~~~  
  D:\Dev\Install\include\boost-1_87\boost\leaf\detail\demangle.hpp(206,15): note: in instantiation of function template specialization 'boost::leaf::n::p<std::error_code>' requested here  
    206 |     return n::p<T>();  
        |               ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\detail\print.hpp(56,38): note: in instantiation of function template specialization 'boost::leaf::parse<std::error_code>' requested here  
     56 |         os << (p ? p : delimiter) << parse<T>();  
        |                                      ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\detail\print.hpp(62,9): note: in instantiation of function template specialization 'boost::leaf::detail::print_name<std::error_code, char, std::char_traits<char>>' requested here  
     62 |         print_name<T>(os, prefix, delimiter);  
        |         ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\detail\print.hpp(110,20): note: in instantiation of function template specialization 'boost::leaf::detail::print_impl<std::error_code, std::error_code, char, std::char_traits<char>>' requested here  
    110 |             return print_impl<Wrapper>(os, prefix, delimiter, ": ", x);  
        |                    ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp(155,36): note: in instantiation of function template specialization 'boost::leaf::detail::diagnostic<std::error_code>::print<char, std::char_traits<char>>' requested here  
    155 |                 if( diagnostic<E>::print(os, prefix, BOOST_LEAF_CFG_DIAGNOSTICS_DELIMITER, value(k)) && !to_print )  
        |                                    ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp(213,23): note: in instantiation of function template specialization 'boost::leaf::detail::slot<std::error_code>::print<char, std::char_traits<char>, boost::leaf::error_id>' requested here  
    213 |                 impl::print(os, to_print, prefix);  
        |                       ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp(218,34): note: in instantiation of member function 'boost::leaf::detail::dynamic_allocator::capturing_slot_node<std::error_code>::print' requested here  
    218 |             BOOST_LEAF_CONSTEXPR capturing_slot_node( capture_list::node * * & last, int err_id, T && e ):  
        |                                  ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp(339,20): note: in instantiation of function template specialization 'boost::leaf::detail::dynamic_allocator::dynamic_load<const std::error_code &>' requested here  
    339 |                 c->dynamic_load(err_id, std::forward<E>(e));  
        |                    ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp(366,17): note: in instantiation of function template specialization 'boost::leaf::detail::dynamic_load_<const std::error_code &>' requested here  
    366 |                 dynamic_load_(err_id, std::forward<E>(e));  
        |                 ^  
  D:\Dev\Install\include\boost-1_87\boost\leaf\error.hpp(442,13): note: in instantiation of function template specialization 'boost::leaf::detail::dynamic_load<false, const std::error_code &>' requested here  
    442 |             dynamic_load<OnError>(err_id, std::forward<E>(e));  
        |             ^  
  4 warnings and 1 error generated.  
```

---

## Comment 1

> Username: Mishura4  
> Created at: 2025-01-27 17:41:06 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2616483359  

Defining `BOOST_LEAF_CFG_CAPTURE=0` can be used to workaround the error.

---

## Comment 2

> Username: zajo  
> Created at: 2025-01-27 17:53:21 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2616509969  

Thank you for reporting, and for the suggested workaround. I'll have it fixed.

---

## Comment 3

> Username: zajo  
> Created at: 2025-01-30 01:29:58 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2623319812  

Judging by the paths, this is on Windows. I need some help.  
  
Is it practical to replace the static_assert in demangle.hpp with something like:  
  
```  
if( !(  
          (s01 && (1 == (!!p01 + !!p02 + !!p03)))  
          ||  
          (s02 && (1 == (!!p04 + !!p05 + !!p06 + !!p07 + !!p08 + !!p09 + !!p10 + !!p11 + !!p12)))  
          ||  
          (s02 && (1 == (!!p13 + !!p14 + !!p15)))  
      ) )  
{  
    std::cout << BOOST_LEAF_PRETTY_FUNCTION;  
}  
```  
  
The idea is instead of triggering a compile error, to print the pretty function. I need to know what it will print so I can update the code.

---

## Comment 4

> Username: Mishura4  
> Created at: 2025-02-03 18:05:15 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2631706926  

Sure I can try in a couple days, and yes this is llvm clang on windows

---

## Comment 5

> Username: zajo  
> Created at: 2025-02-03 22:15:17 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2632255983  

Thanks! Basically I need to know what `__PRETTY_FUNCTION__` expands to in your use case, so I can write the code to parse it. Maybe it has calling convention as part of the name? I don't know.

---

## Comment 6

> Username: Mishura4  
> Created at: 2025-02-17 22:57:38 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2664195970  

Alright, finally got to it, sorry for the delay.  
  
```c++  
  
int main(int argc, char** argv)   
{  
	struct foo {};  
	boost::leaf::parse<foo>();  
}  
```  
`r __cdecl boost::leaf::n::p(void) [T = foo]`

---

## Comment 7

> Username: Mishura4  
> Created at: 2025-02-17 23:23:59 UTC  
> Url: https://github.com/boostorg/leaf/issues/78#issuecomment-2664220633  

Seems easy enough so I just made a PR for it at #79
