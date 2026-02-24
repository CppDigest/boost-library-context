# #207 - clang 3.6 failure after recent develop changes [Closed]

> Username: anarthal  
> Created at: 2024-06-21 15:45:34 UTC  
> Updated at: 2024-06-22 10:01:13 UTC  
> Closed at: 2024-06-21 17:55:56 UTC  
> Url: https://github.com/boostorg/charconv/issues/207  

See https://drone.cpp.al/boostorg/mysql/707/12/3  
  
Very much likely due to 1889937a  
  
Likely fixed by replacing the braces by a an equal sign.  
  
Error:  
  
```  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:651:40: error: direct list initialization of a variable with a deduced type will change meaning in a future version of Clang; insert an '=' to avoid a change in behavior [-Werror,-Wfuture-compat]  
  
            const auto zeros_to_append {static_cast<std::size_t>(value_struct.exponent)};  
  
                                       ^  
  
                                       =  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:653:33: error: invalid operands to binary expression ('const std::initializer_list<unsigned long>' and 'std::size_t' (aka 'unsigned long'))  
  
            if (zeros_to_append > static_cast<std::size_t>(last - r.ptr))  
  
                ~~~~~~~~~~~~~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
./boost/charconv/detail/emulated128.hpp:569:37: note: candidate function not viable: no known conversion from 'const std::initializer_list<unsigned long>' to 'boost::charconv::detail::uint128' for 1st argument  
  
BOOST_CHARCONV_CXX14_CONSTEXPR bool operator>(uint128 lhs, uint128 rhs) noexcept  
  
                                    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_pair.h:233:5: note: candidate template ignored: could not match 'pair' against 'initializer_list'  
  
    operator>(const pair<_T1, _T2>& __x, const pair<_T1, _T2>& __y)  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_iterator.h:310:5: note: candidate template ignored: could not match 'reverse_iterator' against 'initializer_list'  
  
    operator>(const reverse_iterator<_Iterator>& __x,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_iterator.h:360:5: note: candidate template ignored: could not match 'reverse_iterator' against 'initializer_list'  
  
    operator>(const reverse_iterator<_IteratorL>& __x,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_iterator.h:1113:5: note: candidate template ignored: could not match 'move_iterator' against 'initializer_list'  
  
    operator>(const move_iterator<_IteratorL>& __x,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/stl_iterator.h:1119:5: note: candidate template ignored: could not match 'move_iterator' against 'initializer_list'  
  
    operator>(const move_iterator<_Iterator>& __x,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/basic_string.h:5024:5: note: candidate template ignored: could not match 'basic_string' against 'initializer_list'  
  
    operator>(const basic_string<_CharT, _Traits, _Alloc>& __lhs,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/basic_string.h:5036:5: note: candidate template ignored: could not match 'basic_string' against 'initializer_list'  
  
    operator>(const basic_string<_CharT, _Traits, _Alloc>& __lhs,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/basic_string.h:5048:5: note: candidate template ignored: could not match 'const _CharT *' against 'std::initializer_list<unsigned long>'  
  
    operator>(const _CharT* __lhs,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/array:258:5: note: candidate template ignored: could not match 'array' against 'initializer_list'  
  
    operator>(const array<_Tp, _Nm>& __one, const array<_Tp, _Nm>& __two)  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/tuple:947:5: note: candidate template ignored: could not match 'tuple' against 'initializer_list'  
  
    operator>(const tuple<_TElements...>& __t,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/unique_ptr.h:699:5: note: candidate template ignored: could not match 'unique_ptr' against 'initializer_list'  
  
    operator>(const unique_ptr<_Tp, _Dp>& __x,  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/unique_ptr.h:705:5: note: candidate template ignored: could not match 'unique_ptr' against 'initializer_list'  
  
    operator>(const unique_ptr<_Tp, _Dp>& __x, nullptr_t)  
  
    ^  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.4.0/../../../../include/c++/5.4.0/bits/unique_ptr.h:711:5: note: candidate template ignored: could not match 'unique_ptr<type-parameter-0-0, type-parameter-0-1>' against 'unsigned long'  
  
    operator>(nullptr_t, const unique_ptr<_Tp, _Dp>& __x)  
  
    ^  
  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:651:40: error: direct list initialization of a variable with a deduced type will change meaning in a future version of Clang; insert an '=' to avoid a change in behavior [-Werror,-Wfuture-compat]  
  
            const auto zeros_to_append {static_cast<std::size_t>(value_struct.exponent)};  
  
                                       ^  
  
                                       =  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:688:24: note: in instantiation of function template specialization 'boost::charconv::detail::to_chars_fixed_impl<float>' requested here  
  
                return to_chars_fixed_impl(first, last, value, fmt, precision);  
  
                       ^  
  
libs/charconv/build/../src/to_chars.cpp:555:37: note: in instantiation of function template specialization 'boost::charconv::detail::to_chars_float_impl<float>' requested here  
  
    return boost::charconv::detail::to_chars_float_impl(first, last, value, fmt, -1);  
  
                                    ^  
  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:658:37: error: no viable conversion from 'const std::initializer_list<unsigned long>' to 'size_t' (aka 'unsigned long')  
  
            std::memset(r.ptr, '0', zeros_to_append);  
  
                                    ^~~~~~~~~~~~~~~  
  
/usr/include/string.h:62:49: note: passing argument to parameter '__n' here  
  
extern void *memset (void *__s, int __c, size_t __n) __THROW __nonnull ((1));  
  
                                                ^  
  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:659:19: error: invalid operands to binary expression ('char *' and 'const std::initializer_list<unsigned long>')  
  
            r.ptr += zeros_to_append;  
  
            ~~~~~ ^  ~~~~~~~~~~~~~~~  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:651:40: error: direct list initialization of a variable with a deduced type will change meaning in a future version of Clang; insert an '=' to avoid a change in behavior [-Werror,-Wfuture-compat]  
  
            const auto zeros_to_append {static_cast<std::size_t>(value_struct.exponent)};  
  
                                       ^  
  
                                       =  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:688:24: note: in instantiation of function template specialization 'boost::charconv::detail::to_chars_fixed_impl<double>' requested here  
  
                return to_chars_fixed_impl(first, last, value, fmt, precision);  
  
                       ^  
  
libs/charconv/build/../src/to_chars.cpp:572:37: note: in instantiation of function template specialization 'boost::charconv::detail::to_chars_float_impl<double>' requested here  
  
    return boost::charconv::detail::to_chars_float_impl(first, last, value, fmt, -1);  
  
                                    ^  
  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:658:37: error: no viable conversion from 'const std::initializer_list<unsigned long>' to 'size_t' (aka 'unsigned long')  
  
            std::memset(r.ptr, '0', zeros_to_append);  
  
                                    ^~~~~~~~~~~~~~~  
  
/usr/include/string.h:62:49: note: passing argument to parameter '__n' here  
  
extern void *memset (void *__s, int __c, size_t __n) __THROW __nonnull ((1));  
  
                                                ^  
  
In file included from libs/charconv/build/../src/to_chars.cpp:8:  
  
libs/charconv/build/../src/to_chars_float_impl.hpp:659:19: error: invalid operands to binary expression ('char *' and 'const std::initializer_list<unsigned long>')  
  
            r.ptr += zeros_to_append;  
  
            ~~~~~ ^  ~~~~~~~~~~~~~~~  
  
8 errors generated.  
  
  
    "clang++"   -std=c++11 -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -Wextra -Werror -g -fvisibility=hidden -m64 --target=x86_64-pc-linux  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHARCONV_DYN_LINK=1 -DBOOST_CHARCONV_SOURCE=1   -I"."  -c -o "bin.v2/libs/charconv/build/clng-lnx-3.6/dbg/x86_6/cxstd-11-iso/thrd-mlt/vsblt-hdn/to_chars.o" "libs/charconv/build/../src/to_chars.cpp"  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-06-21 16:44:35 UTC  
> Url: https://github.com/boostorg/charconv/issues/207#issuecomment-2183092316  

Should be fixed by the linked PR: https://drone.cpp.al/boostorg/charconv/928/16/2. Since 3.6 and 3.7 work without any additional effort I will leave them in the CI system for now, but I wasn't previously testing anything before 3.8.

---

## Comment 2

> Username: anarthal  
> Created at: 2024-06-22 10:01:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/207#issuecomment-2183966250  

Confirmed the fix. Thanks.
