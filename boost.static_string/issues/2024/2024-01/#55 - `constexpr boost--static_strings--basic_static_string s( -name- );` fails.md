# #55 - `constexpr boost::static_strings::basic_static_string s( "name" );` fails [Closed]

> Username: pdimov  
> Created at: 2024-01-01 19:01:56 UTC  
> Updated at: 2025-12-16 09:49:19 UTC  
> Closed at: 2025-12-16 09:49:19 UTC  
> Url: https://github.com/boostorg/static_string/issues/55  

`constexpr boost::static_strings::basic_static_string s( "name" );` fails to compile (under C++20): https://godbolt.org/z/fvPs3rxa7  
  
Background: in order to write functions that take strings as template arguments, as in `function<"string">()`, one needs to declare the template parameter a static string, because neither `char const*` nor `std::string_view` work.  
  
To avoid needless reinvention of static strings, ours should (probably) be useful in this role. But it isn't, for at least the reason above.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2024-01-02 02:20:03 UTC  
> Url: https://github.com/boostorg/static_string/issues/55#issuecomment-1873571770  

@pdimov This is because of a shortsighted design choice I made to optimize zero size strings, accomplished via a virtual base class. Removing this and making the non-static data members public would allow for it to be used as a NTTP.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-01-02 15:26:38 UTC  
> Url: https://github.com/boostorg/static_string/issues/55#issuecomment-1874170488  

I think this specific problem isn't caused by the class not being structural, though. The error comes from `std::char_traits`.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-01-02 15:27:46 UTC  
> Url: https://github.com/boostorg/static_string/issues/55#issuecomment-1874171929  

Looks like an issue with libstdc++.

---

## Comment 4

> Username: pdimov  
> Created at: 2024-01-02 15:42:30 UTC  
> Url: https://github.com/boostorg/static_string/issues/55#issuecomment-1874191042  

https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113200
