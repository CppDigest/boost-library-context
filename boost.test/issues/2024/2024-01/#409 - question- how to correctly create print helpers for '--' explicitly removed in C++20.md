# #409 - question: how to correctly create print helpers for '<<' explicitly removed in C++20? [Open]

> Username: mbel1  
> Created at: 2024-01-29 14:28:28 UTC  
> Updated at: 2024-04-18 20:38:15 UTC  
> Url: https://github.com/boostorg/test/issues/409  

Hello!  
while project description states that this framework is for C++17 max, could anyone possibly help me with C++20 compatibility issue?  
my problem is that in C++20 operator `<<` for printing wide strings (`std::wstring`, `const wchar_t*, etc) was explicitly [removed](https://en.cppreference.com/w/cpp/io/basic_ostream/operator_ltlt2l). In C++17 I could define helper template like  
```  
template<> std::ostream& operator<<(std::ostream& o, const std::wstring& what) { o << convert_to_char(what); return o; }  
```  
and it worked perfectly. In C++20 this no longer works, as this operator is explicitly deleted, so this template is not even considered. I found that I can partially solve the issue for `std::wstring` by defining this specialization  
```  
namespace boost {  
namespace test_tools {  
namespace tt_detail {  
namespace impl {  
template<> std::ostream& boost_test_print_type_impl::operator()(std::ostream& o, const std::wstring& what) const  
{  
    o << convert_to_char(what); return o;  
}  
} // impl  
} // tt_detail  
} // test_tools  
} // boost  
```  
but this works only for `std::wstring`, not for any wide string literals in a test like  
```  
BOOST_TEST(someWideString == L"expectations");  
```  
as I found no way to match `R const& r` against `wchar_t[12]` so I can't create a specialization in similar manner.  
  
what need to be done, so BOOST_TEST could understand wide string literals in C++20?  
  
if you need compiler specifics: this is Xcode 15 on macOS

---

## Comment 1

> Username: mbel1  
> Created at: 2024-01-30 12:15:33 UTC  
> Url: https://github.com/boostorg/test/issues/409#issuecomment-1916716060  

It turned out that I was incorrect in my analysis of the problem: it turned out that old workaround with `std::ostream& operator<<(std::ostream& o, const std::wstring& what)` works just fine. But with C++20 this workaround no longer works for wide-string literals like `L"expectation"`: when C++ tries to apply `td::ostream& operator<<(std::ostream& o, const wchar_t* what)` it finds that it is deleted, so conversion to `std::wstring` is not considered. So obvious fix is to use wstring's, like  
```  
using std::string_literals;  
BOOST_TEST(someWideString == L"expectations"s);  
```  
but is there a way to make `BOOST_TEST` to work with `const wchar_t*` ?

---

## Comment 2

> Username: zhihaoy  
> Created at: 2024-04-18 20:38:13 UTC  
> Url: https://github.com/boostorg/test/issues/409#issuecomment-2065279375  

> So obvious fix is to use wstring's, like  
>   
> ```  
> using std::string_literals;  
> BOOST_TEST(someWideString == L"expectations"s);  
> ```  
>   
> but is there a way to make `BOOST_TEST` to work with `const wchar_t*` ?  
  
`BOOST_TEST` does work with `const wchar_t*` (not necessarily printing the string); it just doesn't work with `const wchar_t[N]`. `BOOST_TEST` has special handling for pointers to arrays of characters because pointers might be null. Arrays, on the other hand, rely on the presence of `<<`.  
  
My current workaround is  
```cpp  
BOOST_TEST(someWideString == +L"expectations");  
```
