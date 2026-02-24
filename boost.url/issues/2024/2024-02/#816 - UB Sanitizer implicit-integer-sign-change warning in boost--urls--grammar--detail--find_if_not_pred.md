# #816 - UB Sanitizer implicit-integer-sign-change warning in boost::urls::grammar::detail::find_if_not_pred [Closed]

> Username: kevinwichmann  
> Created at: 2024-02-05 15:53:05 UTC  
> Updated at: 2024-02-07 21:02:59 UTC  
> Closed at: 2024-02-07 21:02:59 UTC  
> Url: https://github.com/boostorg/url/issues/816  

Boost: 1.82.0  
Clang: 13  
Ubuntu Linux / x64  
  
Reproducer (with `implicit-integer-sign-change` enabled, `-fsanitize=integer` for example):  
```  
#include <boost/url/parse.hpp>  
int main()  
{  
    (void)boost::urls::parse_relative_ref("/\xff");  
}  
```  
  
Observed warning from Undefined Behavior Sanitizer:  
```  
boost/url/grammar/detail/charset.hpp:171:14: runtime error: implicit conversion from type 'char' of value -1 (8-bit, signed) to type 'unsigned char' changed the value to 255 (8-bit, unsigned)  
    #0 0x52c0903 in char const* boost::urls::grammar::detail::find_if_not_pred<boost::urls::grammar::lut_chars>(boost::urls::grammar::lut_chars const&, char const*, char const*) src.cpp  
    #1 0x52c09ae in boost::system::result<boost::urls::pct_string_view, boost::system::error_code> boost::urls::detail::parse_encoded<boost::urls::grammar::lut_chars>(char const*&, char const*, boost::urls::grammar::lut_chars const&) src.cpp  
    #2 0x52addec in boost::urls::detail::relative_part_rule_t::parse(char const*&, char const*) const src.cpp  
    #3 0x52b1e92 in boost::urls::relative_ref_rule_t::parse(char const*&, char const*) const src.cpp  
    #4 0x52b70f7 in boost::system::result<boost::urls::relative_ref_rule_t::value_type, boost::system::error_code> boost::urls::grammar::parse<boost::urls::relative_ref_rule_t>(boost::core::basic_string_view<char>, boost::urls::relative_ref_rule_t const&) src.cpp  
    #5 0x5296227 in boost::urls::parse_relative_ref(boost::core::basic_string_view<char>) src.cpp  
```  
  
This warning in particular is not about undefined behavior. It's more of a code quality warning (<https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html>):  
```  
-fsanitize=implicit-integer-sign-change: Implicit conversion between integer types, if that changes the sign of the value. That is, if the original value was negative and the new value is positive (or zero), or the original value was positive, and the new value is negative. Issues caught by this sanitizer are not undefined behavior, but are often unintentional.  
```  
  
Origin of the warning in `boost/url/grammar/detail/charset.hpp`:  
```  
template<class Pred>  
char const*  
find_if_not_pred(  
    Pred const& pred,  
    char const* first,  
    char const* last ) noexcept  
{  
[...]  
    while(  
        first != last &&  
        pred(*first)) // <-- warning here  
    {  
        ++first;  
    }  
    return first;  
}  
```  
  
The function being called is declared in `boost/url/grammar/lut_chars.hpp`:  
```  
    constexpr  
    bool  
    operator()(  
        unsigned char ch) const noexcept  
    {  
        return mask_[lo(ch)] & hi(ch);  
    }  
```  
  
These headers haven't been changed on develop since 1.82.0, so my issue should be reproducible with the current develop branch.  
  
Suspected cause of the warning: `*first` is a `char`, but the function takes an `unsigned char`. The value `\xff` as a `char` is represented as `-1`. The implicit conversion to `unsigned char` changes the value to `255` which UB Sanitizer warns about.  
  
Possible fix: `pred(static_cast<unsigned char>(*first))` if that was the original intention.  
  
Workaround: Currently suppressing the warning in Boost.URL with an entry in my suppression file: `implicit-integer-sign-change:/usr/local/include/boost/url/grammar/detail/charset.hpp`.  
  
Suggestion: Run the tests with as many sanitizers warnings enabled as possible.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-02-05 15:55:23 UTC  
> Url: https://github.com/boostorg/url/issues/816#issuecomment-1927315171  

Interesting issue. Thanks. I'll be looking.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2024-02-05 18:20:31 UTC  
> Url: https://github.com/boostorg/url/issues/816#issuecomment-1927701515  

> Suggestion: Run the tests with as many sanitizers warnings enabled as possible.  
  
What's interesting is we are testing clang 14 with asan + ubsan, which should detect the same error.  
  
https://github.com/boostorg/url/actions/runs/7587792728/job/20669035052  
  
I would prefer to be able to replicate the error in CI before moving on with the solution.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-02-05 18:34:06 UTC  
> Url: https://github.com/boostorg/url/issues/816#issuecomment-1927760477  

mmm... according to https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html, `implicit-integer-sign-change` should be enabled with `-fsanitize=undefined`...  
  
> -fsanitize=undefined: All of the checks listed above other than float-divide-by-zero, unsigned-integer-overflow, implicit-conversion, local-bounds and the nullability-* group of checks.  
  
The only warning enabled by `-fsanitize=integer` that shouldn't be enabled by `-fsanitize=undefined` is `unsigned-integer-overflow`  
  
> -fsanitize=integer: Checks for undefined or suspicious integer behavior (e.g. unsigned integer overflow). Enables signed-integer-overflow, unsigned-integer-overflow, shift, integer-divide-by-zero, implicit-unsigned-integer-truncation, implicit-signed-integer-truncation, and implicit-integer-sign-change.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-02-06 02:30:46 UTC  
> Url: https://github.com/boostorg/url/issues/816#issuecomment-1928670575  

I included a job with `-fsanitize=integer` explicitly but still can't replicate it:  
  
https://github.com/boostorg/url/actions/runs/7793196452/job/21252638333?pr=817#step:11:162  
  
What command are you using to build it? Is it cmake or b2?

---

## Comment 5

> Username: kevinwichmann  
> Created at: 2024-02-06 10:36:26 UTC  
> Url: https://github.com/boostorg/url/issues/816#issuecomment-1929227256  

> I included a job with `-fsanitize=integer` explicitly but still can't replicate it:  
>   
> https://github.com/boostorg/url/actions/runs/7793196452/job/21252638333?pr=817#step:11:162  
  
In the section `Test` under `CMake Root Workflow` it says:  
  
```  
  🧪 Tests (C++20)  
  💻 /__w/url/url> /__w/_temp/6c6bf9ea-b5d6-4f29-92d0-1f6ca8af4b27/cmake-3.15.0-Linux-x86_64/bin/ctest --test-dir /__w/url/url/__build_cmake_root__ --parallel 2 --build-config Release --no-tests=error --progress --output-on-failure  
  /__w/_temp/6c6bf9ea-b5d6-4f29-92d0-1f6ca8af4b27/cmake-3.15.0-Linux-x86_64/bin/ctest --test-dir /__w/url/url/__build_cmake_root__ --parallel 2 --build-config Release --no-tests=error --progress --output-on-failure  
  Test project /__w/url/url  
  No tests were found!!!  
```  
  
Could this be an issue?  
  
> What command are you using to build it? Is it cmake or b2?  
  
I am using `b2`:  
```  
./b2 link=static toolset=clang \  
cxxflags="-fPIC -fstack-protector -fstack-check -D_FORTIFY_SOURCE=2 -stdlib=libc++ -v -I/usr/lib/llvm-13/include/c++/v1 -fsanitize=undefined -fsanitize=nullability -fsanitize=float-divide-by-zero -fsanitize=local-bounds -fsanitize=implicit-integer-truncation -fsanitize=implicit-conversion" \  
cxxstd=20 \  
-d1 -j4 \  
--with-thread --with-system --with-chrono --with-date_time --with-filesystem --with-random --with-atomic --with-context --with-url install  
```

---

## Comment 6

> Username: alandefreitas  
> Created at: 2024-02-06 15:55:50 UTC  
> Url: https://github.com/boostorg/url/issues/816#issuecomment-1930107264  

```  
  No tests were found!!!  
```  
  
What the hell! Let me check that. Thanks!
