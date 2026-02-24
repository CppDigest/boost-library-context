# #96 - Implement __has_include [Closed]

> Username: jefftrull  
> Created at: 2020-06-05 17:01:24 UTC  
> Updated at: 2020-06-27 14:22:54 UTC  
> Closed at: 2020-06-27 14:22:54 UTC  
> Url: https://github.com/boostorg/wave/issues/96  

C++17 introduces `__has_include`, which is useful for feature testing. It seems possible to implement it without too much trouble, and at least [one Boost project](https://github.com/joaquintides/boost_epoch) listed its absence as a reason to not use Wave.

---

## Comment 1

> Username: hkaiser  
> Created at: 2020-06-05 17:09:47 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-639638727  

FWIW, this should be easy enough as the required underlying functionality is already available here: https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_include_paths.hpp#L350

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-06-17 01:35:02 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-645095129  

So, would you call `__has_include` a "function-like macro" or is it similar to `__VA_OPT__` where because it is not usable in a general context (i.e. outside of `#if` or `#elif`) it doesn't count? I'm thinking the latter...

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-06-17 02:00:20 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-645101690  

`__has_include` is special. It's both, a function like and an object like macro that can be used in `#if` expressions only (see for instance https://gcc.gnu.org/onlinedocs/gcc-10.1.0/cpp/_005f_005fhas_005finclude.html).

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-06-17 02:05:20 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-645102995  

Oh boy :) So do we fire the expanding-macro hooks then?

---

## Comment 5

> Username: hkaiser  
> Created at: 2020-06-17 12:48:28 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-645352725  

@jefftrull This is tricky. From what I read in the Standard (http://eel.is/c++draft/cpp.cond#nt:has-include-expression), there are two forms of the function-like invocation:  
```  
_­_­has_­include ( header-name )  
_­_­has_­include ( header-name-tokens )  
```  
it also says:  
> 2. The second form of `has-include-expression` is considered only if the first form does not match, in which case the preprocessing tokens are processed just as in normal text.  
> 3. The header or source file identified by the parenthesized preprocessing token sequence in each contained `has-include-expression` is searched for as if that preprocessing token sequence were the pp-tokens in a #include directive, except that no further macro expansion is performed. If such a directive would not satisfy the syntactic requirements of a #include directive, the program is ill-formed. The `has-include-expression` evaluates to 1 if the search for the source file succeeds, and to 0 if the search fails.  
  
I would read 3. such that only the two (common) forms of valid `#include` statement syntaxes have to be supported here:  
```  
h-preprocessing-token:  
    any preprocessing-token other than >  
h-pp-tokens:  
    h-preprocessing-token  
    h-pp-tokens h-preprocessing-token  
header-name-tokens:  
    string-literal  
    < h-pp-tokens >  
```  
It explicitly says that `no further macro expansion is performed`. There might be no room for invoking any of the hooks after all (we don't invoke any hooks for the `defined()` operator either).  
  
For all of the above I'd love to hear other opinions, however.

---

## Comment 6

> Username: jefftrull  
> Created at: 2020-06-17 15:56:08 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-645461889  

I personally feel like it's most similar to `defined()` and should be treated the same way.

---

## Comment 7

> Username: jefftrull  
> Created at: 2020-06-20 00:10:14 UTC  
> Updated at: 2020-06-20 00:11:10 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-646903572  

It looks like - if I read this correctly - we have to perform expansion, _once_, on the tokens inside the parentheses of `__has_include`, in a similar manner to the code [here](https://github.com/boostorg/wave/blob/4e6c4fbd2e942267b2bcd1053d768fe802f597e1/include/boost/wave/util/cpp_iterator.hpp#L1718) if the expression is not a plain quoted or angle-bracketed include expression - does that sound right?

---

## Comment 8

> Username: hkaiser  
> Created at: 2020-06-20 01:10:39 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-646912840  

> It looks like - if I read this correctly - we have to perform expansion, once, on the tokens inside the parentheses of __has_include, in a similar manner to the code here if the expression is not a plain quoted or angle-bracketed include expression - does that sound right?  
  
I'm not sure. That's what I was trying to discuss above:  
  
> I would read 3. such that only the two (common) forms of valid #include statement syntaxes have to be supported here:  
> ```  
> h-preprocessing-token:  
>     any preprocessing-token other than >  
> h-pp-tokens:  
>     h-preprocessing-token  
>     h-pp-tokens h-preprocessing-token  
> header-name-tokens:  
>     string-literal  
>     < h-pp-tokens >  
> ```  
  
This sounds to me as if `__has_include()` supports only quoted strings and filenames in angle brackets. How does clang handle this?

---

## Comment 9

> Username: jefftrull  
> Created at: 2020-06-20 05:51:42 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-646946768  

I read this a bit differently - I think the first case (`header-name`) contains the normal double quoted _or_ angle bracketed options, while the second part (`header-name-tokens`) is either a string literal or `< h-pp-tokens >`, which can be just about anything enclosed in angle brackets that can evaluate to a valid filename - I assume that's what the code [here](https://github.com/boostorg/wave/blob/4e6c4fbd2e942267b2bcd1053d768fe802f597e1/include/boost/wave/util/cpp_iterator.hpp#L1718) is doing for `#include`. In other words, a "computed include" is allowed here, which is why we may have to perform macro expansion...?  
  
I'll try to create an example.

---

## Comment 10

> Username: hkaiser  
> Created at: 2020-06-20 14:04:58 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-647000048  

Any #include statement can have three forms: `#include "string-literal" new-line`, `#include <h-pp-tokens> new-line`, or `#include pp-tokens new-line`. The decision is made based on whether it's something surrounded by `""`, or by `<>`. The third option is a fallback if neither of the first apply (see http://eel.is/c++draft/cpp.include). Only for the third option the pp-tokens following the `#include` are macro-expanded.  
  
The specification for `__has_include( something )` says that `something` can be either a string-literal (i.e. something surrounded by `""` or a sequence of pp-tokens surrounded by `<>` where `>` is not allowed in between the `<>`:  
```  
has-include-expression:  
    _­_­has_­include ( header-name )  
    _­_­has_­include ( header-name-tokens )  
```  
which is confusing to me, as both, the `header-name` (http://eel.is/c++draft/lex.header#nt:header-name) and the `header-name-tokens` (http://eel.is/c++draft/cpp.cond#nt:header-name-tokens) are defined the same.   
  
In any case, I think that none of the definitions above even hint at the third option (namely some sequence of pp-tokens that needs to be macro preprocessed). This is also supported by (see above, highlighted by me):  
  
> The header or source file identified by the parenthesized preprocessing token sequence in each contained `has-include-expression` is searched for as if that preprocessing token sequence were the pp-tokens in a #include directive, except that **no further macro expansion is performed**.  
  
However, I admit that all of this is not conclusive (as usual when you start reading the standard) and I might be wrong. Let's find out what clang and gcc do and go for the same.

---

## Comment 11

> Username: jefftrull  
> Created at: 2020-06-20 17:25:29 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-647023862  

Here's an example that works in both gcc and clang:  
  
```c++  
#define FOO(X, Y) <X ## Y>  
  
#if __has_include( FOO(io,stream) )  
char const * result = "do";  
#else  
char const * result = "do not";  
#endif  
  
#include FOO(io,stream)  
  
int main()  
{  
    std::cout << "we " << result << " have iostream\n";  
}  
```

---

## Comment 12

> Username: jefftrull  
> Created at: 2020-06-20 17:30:34 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-647024451  

As for the Standard, my reading is that  
  
`_­_­has_­include ( header-name )` incorporates the two standard forms (quotes and brackets) of file inclusion, while  
  
`_­_­has_­include ( header-name-tokens )` gives an additional form usable for "computed includes".

---

## Comment 13

> Username: jefftrull  
> Created at: 2020-06-20 18:27:00 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-647030216  

I've found an interesting inconsistency between gcc and clang:  
```c++  
#define BAR iostream  
#define FOO <BAR>  
  
// clang says <BAR> is not found  
// gcc says it is  
#if __has_include( <BAR> )  
char const * result = "do";  
#else  
char const * result = "do not";  
#endif  
  
// both clang and gcc are happy with this:  
#include FOO  
  
int main()  
{  
    std::cout << "we " << result << " have iostream\n";  
}  
```

---

## Comment 14

> Username: jefftrull  
> Created at: 2020-06-20 18:55:10 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-647033162  

Actually it looks like Clang is correct. As the standard says:  
> The second form of has-include-expression is considered only if the first form does not match.  
  
So because `<BAR>` looks like a regular include filename, it searches for it and does not try to expand the macro.  
  
[Here](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80005)'s a really interesting gcc bugzilla thread for the problem (it was fixed in 10.1, which I don't have on my system).

---

## Comment 15

> Username: jefftrull  
> Created at: 2020-06-20 19:01:17 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-647033769  

This line (from Bugzilla) seems particularly relevant:  
  
>The proper fix, IMO, would be to apply the same special tokenization rules to the argument of __has_include that are used for the argument of #include. This is not exactly the same as "don't macro-expand the argument."

---

## Comment 16

> Username: jefftrull  
> Created at: 2020-06-24 22:06:17 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-649098138  

Here's an interesting puzzle:  
The gcc manual says that includes get special treatment in quoting, e.g. `#include "x\n\\y"` _specifies a filename containing three backslashes_. Currently AFAICT this is handled at the lexer level by using different tokens (`QHEADER` and `HHEADER`). Would we need to introduce a new token for `__has_include` ?

---

## Comment 17

> Username: hkaiser  
> Created at: 2020-06-24 22:41:34 UTC  
> Url: https://github.com/boostorg/wave/issues/96#issuecomment-649110717  

> Here's an interesting puzzle:  
> The gcc manual says that includes get special treatment in quoting, e.g. #include "x\n\\y" specifies a filename containing three backslashes. Currently AFAICT this is handled at the lexer level by using different tokens (QHEADER and HHEADER). Would we need to introduce a new token for __has_include ?  
  
Whatever we do, we should have consistent behavior for `#include` and `__has_include`.
