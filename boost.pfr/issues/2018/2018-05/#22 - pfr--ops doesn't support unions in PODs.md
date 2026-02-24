# #22 - pfr::ops doesn't support unions in PODs [Closed]

> Username: anthonygclark  
> Created at: 2018-05-21 22:12:46 UTC  
> Updated at: 2018-08-07 01:57:08 UTC  
> Closed at: 2018-06-12 10:06:45 UTC  
> Url: https://github.com/boostorg/pfr/issues/22  

First off, I love this library. I find myself writing a lot of binary protocols that support unions. One thing I do to  test out my binary parsers/generators, is just sending the instance to `operator<<` and not having to write a 100+ line function myself, I love this (ehm, Rust has this as well).  
  
Any ideas on how you could support unions? Maybe just convert to `std::tuple<>` and print that? I've gotten `boost::hana` to play nice sometimes but nothing notable. I think if you support unions, lots of C code or legacy C++ could be brought to a new light by just automatically having `pfr::ops` work.  
  
Thanks!

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-05-24 17:39:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-391799364  

I've added some tests for unions  in e7abff6 and they seem to work.  
  
Could you please provide some usage examples that do not work?

---

## Comment 2

> Username: anthonygclark  
> Created at: 2018-05-24 18:37:07 UTC  
> Updated at: 2018-05-24 18:38:33 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-391817050  

@apolukhin Thanks for the follow-up. Maybe I should have clarified. I don't think the automatic `operator<<(ostream &,...)` works if the POD has a union. I should note that this might be a limitation of the language, or maybe you intended this be a limitation.  
  
I took the example from the README and added a union:  
  
```cpp  
// requires: C++14  
#include <iostream>  
#include <string>  
#include "boost/pfr/precise.hpp"  
  
union foo {  
    int x; float y;  
};  
  
struct some_person {  
    std::string name;  
    unsigned birth_year;  
    foo f;  
};  
  
int main() {  
    some_person val{"Edgar Allan Poe", 1809};  
  
    std::cout << boost::pfr::get<0>(val)                // No macro!  
        << " was born in " << boost::pfr::get<1>(val);  // Works with any aggregate initializables!  
}  
```  
  
Here is the output:  
```sh  
clang version 6.0.0 (tags/RELEASE_600/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
```sh  
In file included from anthony.cc:2:  
In file included from include/boost/pfr/precise.hpp:14:  
In file included from include/boost/pfr/precise/ops.hpp:15:  
In file included from include/boost/pfr/precise/io.hpp:17:  
include/boost/pfr/detail/io.hpp:51:13: error: invalid operands to binary expression ('std::basic_ostream<char>' and 'const foo')  
        out << quoted_helper(boost::pfr::detail::sequence_tuple::get<I>(value));  
        ~~~ ^  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
include/boost/pfr/detail/io.hpp:52:31: note: in instantiation of function template specialization 'boost::pfr::detail::print_impl<3, 4>::print<std::basic_ostream<char>, boost::pfr::detail::sequence_tuple::tuple<const int &, const char &, const double &, const foo &> >'  
      requested here  
        print_impl<I + 1, N>::print(out, value);  
                              ^  
include/boost/pfr/detail/io.hpp:52:31: note: in instantiation of function template specialization 'boost::pfr::detail::print_impl<2, 4>::print<std::basic_ostream<char>, boost::pfr::detail::sequence_tuple::tuple<const int &, const char &, const double &, const foo &> >'  
      requested here  
include/boost/pfr/detail/io.hpp:52:31: note: in instantiation of function template specialization 'boost::pfr::detail::print_impl<1, 4>::print<std::basic_ostream<char>, boost::pfr::detail::sequence_tuple::tuple<const int &, const char &, const double &, const foo &> >'  
      requested here  
include/boost/pfr/precise/io.hpp:50:54: note: in instantiation of function template specialization 'boost::pfr::detail::print_impl<0, 4>::print<std::basic_ostream<char>, boost::pfr::detail::sequence_tuple::tuple<const int &, const char &, const double &, const foo &> >'  
      requested here  
            detail::print_impl<0, fields_count_val>::print(out, val);  
                                                     ^  
include/boost/pfr/precise/io.hpp:47:27: note: in instantiation of function template specialization 'boost::pfr::detail::for_each_field_dispatcher<const my_struct, (lambda at include/boost/pfr/precise/io.hpp:49:9), 0, 1, 2, 3>' requested here  
    ::boost::pfr::detail::for_each_field_dispatcher(  
                          ^  
include/boost/pfr/precise/ops.hpp:129:21: note: in instantiation of function template specialization 'boost::pfr::write<char, std::char_traits<char>, my_struct>' requested here  
        boost::pfr::write(out, value);  
                    ^  
anthony.cc:23:24: note: in instantiation of function template specialization 'boost::pfr::ops::operator<<<char, std::char_traits<char>, my_struct>' requested here  
        << " fields: " << s << "\n";  
                       ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:245:7: note: candidate function not viable: no known conversion from 'const foo' to 'const void *' for 1st argument; take the address of the argument with &  
      operator<<(const void* __p)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/system_error:217:5: note: candidate function not viable: no known conversion from 'const foo' to 'const std::error_code' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __os, const error_code& __e)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:108:7: note: candidate function not viable: no known conversion from 'const foo' to 'std::basic_ostream<char, std::char_traits<char> >::__ostream_type &(*)(std::basic_ostream<char,  
      std::char_traits<char> >::__ostream_type &)' (aka 'basic_ostream<char, std::char_traits<char> > &(*)(basic_ostream<char, std::char_traits<char> > &)') for 1st argument  
      operator<<(__ostream_type& (*__pf)(__ostream_type&))  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:117:7: note: candidate function not viable: no known conversion from 'const foo' to 'std::basic_ostream<char, std::char_traits<char> >::__ios_type &(*)(std::basic_ostream<char,  
      std::char_traits<char> >::__ios_type &)' (aka 'basic_ios<char, std::char_traits<char> > &(*)(basic_ios<char, std::char_traits<char> > &)') for 1st argument  
      operator<<(__ios_type& (*__pf)(__ios_type&))  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:127:7: note: candidate function not viable: no known conversion from 'const foo' to 'std::ios_base &(*)(std::ios_base &)' for 1st argument  
      operator<<(ios_base& (*__pf) (ios_base&))  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:166:7: note: candidate function not viable: no known conversion from 'const foo' to 'long' for 1st argument  
      operator<<(long __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:170:7: note: candidate function not viable: no known conversion from 'const foo' to 'unsigned long' for 1st argument  
      operator<<(unsigned long __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:174:7: note: candidate function not viable: no known conversion from 'const foo' to 'bool' for 1st argument  
      operator<<(bool __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:178:7: note: candidate function not viable: no known conversion from 'const foo' to 'short' for 1st argument  
      operator<<(short __n);  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:181:7: note: candidate function not viable: no known conversion from 'const foo' to 'unsigned short' for 1st argument  
      operator<<(unsigned short __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:189:7: note: candidate function not viable: no known conversion from 'const foo' to 'int' for 1st argument  
      operator<<(int __n);  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:192:7: note: candidate function not viable: no known conversion from 'const foo' to 'unsigned int' for 1st argument  
      operator<<(unsigned int __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:201:7: note: candidate function not viable: no known conversion from 'const foo' to 'long long' for 1st argument  
      operator<<(long long __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:205:7: note: candidate function not viable: no known conversion from 'const foo' to 'unsigned long long' for 1st argument  
      operator<<(unsigned long long __n)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:220:7: note: candidate function not viable: no known conversion from 'const foo' to 'double' for 1st argument  
      operator<<(double __f)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:224:7: note: candidate function not viable: no known conversion from 'const foo' to 'float' for 1st argument  
      operator<<(float __f)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:232:7: note: candidate function not viable: no known conversion from 'const foo' to 'long double' for 1st argument  
      operator<<(long double __f)  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:270:7: note: candidate function not viable: no known conversion from 'const foo' to 'std::basic_ostream<char, std::char_traits<char> >::__streambuf_type *'  
      (aka 'basic_streambuf<char, std::char_traits<char> > *') for 1st argument  
      operator<<(__streambuf_type* __sb);  
      ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:502:5: note: candidate function not viable: no known conversion from 'const foo' to 'char' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __out, char __c)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:508:5: note: candidate function not viable: no known conversion from 'const foo' to 'char' for 2nd argument  
    operator<<(basic_ostream<char, _Traits>& __out, char __c)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:514:5: note: candidate function not viable: no known conversion from 'const foo' to 'signed char' for 2nd argument  
    operator<<(basic_ostream<char, _Traits>& __out, signed char __c)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:519:5: note: candidate function not viable: no known conversion from 'const foo' to 'unsigned char' for 2nd argument  
    operator<<(basic_ostream<char, _Traits>& __out, unsigned char __c)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:556:5: note: candidate function not viable: no known conversion from 'const foo' to 'const char *' for 2nd argument  
    operator<<(basic_ostream<char, _Traits>& __out, const char* __s)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:569:5: note: candidate function not viable: no known conversion from 'const foo' to 'const signed char *' for 2nd argument  
    operator<<(basic_ostream<char, _Traits>& __out, const signed char* __s)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:574:5: note: candidate function not viable: no known conversion from 'const foo' to 'const unsigned char *' for 2nd argument  
    operator<<(basic_ostream<char, _Traits>& __out, const unsigned char* __s)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/bits/ostream.tcc:321:5: note: candidate function not viable: no known conversion from 'const foo' to 'const char *' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __out, const char* __s)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:79:5: note: candidate function not viable: no known conversion from 'const foo' to 'std::_Resetiosflags' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Resetiosflags __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:109:5: note: candidate function not viable: no known conversion from 'const foo' to 'std::_Setiosflags' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Setiosflags __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:143:5: note: candidate function not viable: no known conversion from 'const foo' to 'std::_Setbase' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Setbase __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:208:5: note: candidate function not viable: no known conversion from 'const foo' to 'std::_Setprecision' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Setprecision __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:238:5: note: candidate function not viable: no known conversion from 'const foo' to 'std::_Setw' for 2nd argument  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Setw __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:497:5: note: candidate template ignored: deduced conflicting types for parameter '_CharT' ('char' vs. 'foo')  
    operator<<(basic_ostream<_CharT, _Traits>& __out, _CharT __c)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/bits/basic_string.h:6284:5: note: candidate template ignored: could not match 'basic_string<type-parameter-0-0, type-parameter-0-1, type-parameter-0-2>' against 'const foo'  
    operator<<(basic_ostream<_CharT, _Traits>& __os,  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:539:5: note: candidate template ignored: could not match 'const _CharT *' against 'foo'  
    operator<<(basic_ostream<_CharT, _Traits>& __out, const _CharT* __s)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/ostream:682:5: note: candidate template ignored: requirement '__and_<__not_<is_lvalue_reference<basic_ostream<char> &> >, __is_convertible_to_basic_ostream<basic_ostream<char> &>,  
      __is_insertable<__rvalue_ostream_type<basic_ostream<char> &>, const foo &> >::value' was not satisfied [with _Ostream = std::basic_ostream<char> &, _Tp = foo]  
    operator<<(_Ostream&& __os, const _Tp& __x)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:178:5: note: candidate template ignored: could not match '_Setfill<type-parameter-0-0>' against 'foo'  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Setfill<_CharT> __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:311:5: note: candidate template ignored: could not match '_Put_money<type-parameter-0-2>' against 'foo'  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Put_money<_MoneyT> __f)  
    ^  
/usr/bin/../lib64/gcc/x86_64-pc-linux-gnu/8.1.0/../../../../include/c++/8.1.0/iomanip:363:5: note: candidate template ignored: could not match '_Put_time<type-parameter-0-0>' against 'foo'  
    operator<<(basic_ostream<_CharT, _Traits>& __os, _Put_time<_CharT> __f)  
    ^  
1 error generated.  
```

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-05-24 19:27:30 UTC  
> Updated at: 2018-05-24 19:34:52 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-391831266  

Unfortunately I have no idea to how to do it right.  
  
When we are working with simple unions like  
```  
union foo {  
    int x; float y;  
};  
```  
  
it is just possible to output the `x` or `y` variable. But in more complex cases that could lead to segfaults or some other undefined behaviors.   
  
  
Consider the following union:  
```  
union bar {  
    std::string x; std::set<int> y;  
};  
```  
  
If the last active member is `y` and we are accessing `x`, then there'll be a segfault.  
  
  
So, I'd rather totally disable the reflection of unions to avoid possible segfaults. Unless you have a better idea.

---

## Comment 4

> Username: anthonygclark  
> Created at: 2018-05-24 19:36:23 UTC  
> Updated at: 2018-05-24 19:36:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-391833751  

@apolukhin Yea, I guess having anything nested causes issues. That doesn't seem like a defect with your library. Though making the claim that you can generate an `operator<<(ostream &, ...)` for all PODs would then be untrue as PODs can be nested. ie - `std::is_pod<some_person>::value == true`. I'm not even sure this is possible.  
  
I really appreciate the help @apolukhin.

---

## Comment 5

> Username: apolukhin  
> Created at: 2018-05-24 19:39:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-391834665  

I'll do some experiments and will try to come up with something reasonable. Probably you're right and there's some way to reflect unions safe and right.

---

## Comment 6

> Username: apolukhin  
> Created at: 2018-06-01 20:26:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-393999677  

I've started by totally disabling the reflection for unions.  
  
Here are some unsolvable issues:  
* There's a way to reflect the first member of a union and use it. Unfortunately there's no way to find out *active* member of a union. Usually this is OK to use the first one, thou strictly speaking it's an UB  
* Using the first union member for reflection could lead to disaster if it is some character pointer. For example ostreaming union {char* c; long long ll; } u; u.ll= 0; will crash your program, as the active member is ll that holds a 0 but we are trying to output a char*. This would cause a nullptr dereference.  
* Even if the first union member is not a  character pointer, we could get hit by many other cases. For example getting the non active double union member could produce a signaling nan, or hurt the application in some other nasty way.  
  
I'm in favor of leaving the reflection for unions disabled and producing a better static_assert message with hints on how to fix the issue.

---

## Comment 7

> Username: cbeck88  
> Created at: 2018-08-03 14:49:00 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-410277229  

@anthonygclark I think you might want to look into using a variant type like `boost::variant` instead of `union`, and then it would support streaming operator directly and correctly

---

## Comment 8

> Username: anthonygclark  
> Created at: 2018-08-07 01:32:39 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-410903847  

@cbeck88  
  
The issue is legacy. If I were writing all new code, then all would be well. But I’m trying to find a nice way to display large (sometimes 50+) structures that are spewed over a wire. For this, lots of hardware uses packed structures, unions,  and unaligned arrays. I’m thinking a tool to spit out an unsafe printf call from a structure definition is probably better.   
  
Btw, an std::variant and union are not really the same physically so variant doesn’t apply.  
  
> On Aug 3, 2018, at 10:56 AM, Chris Beck <notifications@github.com> wrote:  
>   
> @anthonygclark I think you might want to look into using a variant type like boost::variant instead of union, and then it would support streaming operator directly and correctly  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 9

> Username: cbeck88  
> Created at: 2018-08-07 01:40:38 UTC  
> Updated at: 2018-08-07 01:57:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/22#issuecomment-410905076  

> For this, lots of hardware uses packed structures, unions,  and unaligned arrays.  
  
`magic_get` works by using some template trickery to extract, given a struct, the types (and order) of the members, then it uses internal logic to deduce the offsets of the members based on that information. It used to work by simply reinterpret_cast'ing your struct as an "equivalent" tuple, it now does something only slightly different that is standards compliant.  
  
If you have packed structs, i.e. with `__attribute__((packed))` I'd say it has a snowball's chance in hell of working anyways, because all the offsets are going to be wrong. There's no way for a template to determine that your struct is marked with attribute packed. Actually I thought we had a `static_assert` that the size we expect based on the layout that we compute is the same as the true size of the struct, which would handle the "packed" case.  
  
I guess we could try to fix it by checking if your struct has the "packed" size or the "unpacked" size, and then "deduce" what the layout is that way.  
  
Given that `__attribute__((packed))` is kind of squirrelly and implementation defined anyways, and this could make it harder to detect corruption, I wouldn't think that most users would want that to be on by default anyways.  
  
I wouldn't expect unaligned arrays to work either for the same reason. Anything that screws with the layout of the struct is going to cause a big problem. So, I would expect you have much bigger fish to fry than the unions in the case you describe.
