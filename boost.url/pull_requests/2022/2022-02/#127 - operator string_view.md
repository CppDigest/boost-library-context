# #127 operator string_view [Closed]

> Username: alandefreitas  
> Created at: 2022-02-19 20:54:30 UTC  
> Updated at: 2022-07-05 16:03:20 UTC  
> Closed at: 2022-03-11 22:49:58 UTC  
> Url: https://github.com/boostorg/url/pull/127  

### This PR:  
  
- implements `const_string::operator string_view()`, `const_string::operator std::basic_string<...>()`, and all other conversion operators defined by `boost::core::string_view` to allow conversions without ambiguity.  
- implements the semantically valid subset of `string_view` as member functions of `const_string`   
- removes `string_view` as a base class of `const_string`.  
  
### Benefits:  
  
- Design:  
    - Remove public derivation from a concrete type ([ref](https://stackoverflow.com/questions/16724946/why-derive-from-a-concrete-class-is-a-poor-design))  
    - Removes a base class and its subset of functions in conflict with the semantics of `const_string`  
    - Functions from `string_view` that are in conflict with `const_string`, such as `remove_suffix`, are removed from the interface  
    - Simplify implementation details requiring `static_cast<string_view&>` to avoid warnings ([ref](https://github.com/alandefreitas/url/blob/21ce2dc6cbb154bfc16a28cd77d90f6aca87d2d9/include/boost/url/impl/const_string.hpp#L180))  
- User:  
    - Documentation warning users about undefined behavior for these functions become unnecessary and are removed  
    - Users will not get warnings about slicing being unsafe when using `const_string`  
- Memory allocations:  
    - Enables optimization where the pointer is not stored redundantly in the shared string and the base `string_view`  
    - Enables optimization where the pointer is not stored redundantly in the base `string_view` while it's known to be at the first position of the built-in buffer  
    - Enables optimization where the size is not stored redundantly in the shared string and the base `string_view`  
    - Enables optimization where the component does not need encoding and acts as a _constant_ reference to the underlying URL, since `const_string` is used by lazy generators where its lifetime does not go beyond the `url_view` (renaming might be advised in this case).  
    - Enables optimization where the size does not take more memory than it needs for the maximum of `2048` characters  allowed in a URL  
  
### Cons:  
  
- More code to replicate the string_view functionality. On the other hand, all of these functions are one-liners forwarding to `string_view` or returning pointers. (see [ref](https://github.com/alandefreitas/url/blob/21ce2dc6cbb154bfc16a28cd77d90f6aca87d2d9/include/boost/url/const_string.hpp#L264))  
- Requires workaround for a few older compilers (somewhere around GCC >5.0 <= 6.0) that provide initial support for `std::string_view` but consider its conversions ambiguous (see [ref](https://github.com/alandefreitas/url/blob/21ce2dc6cbb154bfc16a28cd77d90f6aca87d2d9/include/boost/url/detail/config.hpp#L96))  
- _Maybe_ there's a solution based on inheritance where `remove_suffix` and friends are not undefined behavior.  
  
### Before/after table:  
  
<table>  
<tr>  
<th>  
Without PR122  
</th>  
<th>  
With PR122  
</th>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
url_view u = /* ... */;  
std::cout << u;   
// user gets warning about object slicing  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
url_view u = /* ... */;  
std::cout << u;  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
static_assert(  
    sizeof(const_string{}) ==   
    builtin_capacity + 32);  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
static_assert(  
    sizeof(const_string{}) ==   
    builtin_capacity + 16);  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
// no optimizations possible  
static_assert(  
    sizeof(const_string{}) ==   
    builtin_capacity + 32);  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
// with trivial optimizations  
static_assert(  
    sizeof(const_string{}) ==   
    builtin_capacity);  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
void break_stuff(urls::string_view& a) {  
    urls::string_view b{};  
    // only the urls::string_view part  
    // of a will be modified  
    a = b;  
}  
break_stuff(const_string{});  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
void break_stuff(urls::string_view& a) {  
    urls::string_view b{};  
    a = b;  
}  
// This doesn't compile:  
break_stuff(const_string{});  
</pre>  
</td>  
</tr>  
  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
struct A  
{  
    A( std::string_view );  
};  
struct B  
{  
    B( urls::string_view );  
};  
// This works:  
A(const_string{});  
B(const_string{});  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
struct A  
{  
    A( std::string_view );  
};  
struct B  
{  
    B( urls::string_view );  
};  
// This still works:  
A(const_string{});  
B(const_string{});  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
// Copy-initialization doesn't work:  
A = const_string{};  
B = const_string{};  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
// Copy-initialization still doesn't work:  
A = const_string{};  
B = const_string{};  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
// Copy-initialization doesn't work:  
A = const_string{};  
B = const_string{};  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
// Copy-initialization still doesn't work:  
A = const_string{};  
B = const_string{};  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
void f1( std::string_view );  
void f2( urls::string_view );  
void f3( std::string const& );  
void f4( std::basic_string<  
                 char,   
                 std::char_traits<char>,   
                 custom_allocator<char>> const& );  
// This works:  
f1(const_string{});  
f2(const_string{});  
f3(const_string{});  
f4(const_string{});  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
void f1( std::string_view );  
void f2( urls::string_view );  
void f3( std::string const& );  
void f4( std::basic_string<  
                 char,   
                 std::char_traits<char>,   
                 custom_allocator<char>> const& );  
// This still works:  
f1(const_string{});  
f2(const_string{});  
f3(const_string{});  
f4(const_string{});  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
struct A  
{  
    A( std::string_view );  
};  
void f( A const& );  
// This fails:  
f(const_string{});  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
struct A  
{  
    A( std::string_view );  
};  
void f( A const& );  
// This still fails:  
f(const_string{});  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
// in implementation  
static_cast<string_view&>(*this  
            ) = { data_.buf_, s.size()};  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
// in implementation  
str_ = data_.buf_;  
len_ = s.size();  
</pre>  
</td>  
</tr>  
  
<tr>  
<td  valign="top">  
<pre lang="cpp">  
// in implementation  
/*  
 * nothing  
 */  
</pre>  
</td>  
<td  valign="top">  
  
<pre lang="cpp">  
// in implementation  
compare(string_view other) const  
{  
    return operator string_view().compare(other);  
}  
</pre>  
</td>  
</tr>  
  
</table>  
  
### Tests:  
  
The unit tests ensure all cases of ambiguity discussed on January 20th have been fixed:  
  
- Use `const_string` in functions expecting `std::string`: https://godbolt.org/z/ahbf3P85h / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L393)  
- Use `const_string` to copy initialize `std::string`: https://godbolt.org/z/a9rjq716K / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L403)  
- Use `const_string` to construct `std::string`: https://godbolt.org/z/3jPeaP67e / https://godbolt.org/z/9hEa38hxh / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L400)  
- Use `const_string` in functions expecting `std::string_view`: https://godbolt.org/z/a8KPj8G5K / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L387)  
- Use `const_string` to construct classes analogous to `boost::json::string` that are construtible from `string_view`, _as requested on January 20th but that didn't work before this PR_: https://godbolt.org/z/a8KPj8G5K / https://godbolt.org/z/13nxWKdM4 / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L416) / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L431)  
  
### About copy-initialization:  
  
Copy-initialization is the only scenario where we can't achieve implicit conversions in this PR.   
  
The rules for implicit conversions in copy-initialization (`A = B{};`) are different from the rules for direct-initialization (`A(B);`). Basically, copy-initialization supports no implicit conversions while direct-initialization supports at most one implicit conversion.   
  
This means neither inheritance nor conversion operators work for copy initialization (A = const_string{};) properly, unless we inherit from _both_ `std::string_view` and `boost::core::string_view`, store the same data redundantly _3_ times, and it still wouldn't work with `std::basic_string<char, A>`. ([ref](https://godbolt.org/z/KExojPvxW))  
  
In more detail:   
  
- Objects that (i) are not `std::string_view`, `core::string_view` or `std::basic_string`; and (ii) are implicitly constructible from `string_view` or `std::string_view` cannot be copy initialized from `const_string`.   
- This is not a problem for constructors and functions that expect implicit conversions.  
- This problem happens for both `operator string_view` and for `string_view` as a base class, unless `const_string` would inherit from both `string_view` _and_ `std::string_view` just to make this work.  
- The [reason](https://en.cppreference.com/w/cpp/language/copy_initialization#Notes) is "the implicit conversion in copy-initialization must produce T directly from the initializer, while, e.g. direct-initialization expects an implicit conversion from the initializer to an argument of T's constructor"  
- The only solution would be an implicit conversion from `const_string` to any type `T` constructible from `string_view`, which leads to a lot of ambiguity.   
    - Some newer compilers prefer the overload closest to the copy constructor and don't fail.   
    - Most compilers consider that ambiguous with all other constructors from types from where we can create a string_view.   
    - For instance, `ipv6_address(ipv6_address const&)`, `ipv6_address(ipv4_address const&)`, and `ipv6_address(string_view)` would be ambiguous because we can convert a const_string to any of them.  
    - We _could_ write workarounds to isolate and support the compilers where this works. It doesn't seem like it's worth leading the user in this direction.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-02-19 21:24:53 UTC  
> Updated_at: 2022-02-19 21:25:23 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1046105975  

> There might be use cases that do not work without inheriting from string_view. No such case has been found in the existing tests and in the new tests.  
  
But https://godbolt.org/z/8cd31xE74

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-02-19 23:52:04 UTC  
> Updated_at: 2022-02-20 23:37:01 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1046126326  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#127](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (21ce2dc) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/3dba527bb31d9fdfb0ac93d4794c2ebf9b9d61ae?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (3dba527) will **increase** coverage by `0.03%`.  
> The diff coverage is `98.42%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/127/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #127      +/-   ##  
===========================================  
+ Coverage    96.39%   96.42%   +0.03%       
===========================================  
  Files          104      104                
  Lines         5485     5595     +110       
===========================================  
+ Hits          5287     5395     +108       
- Misses         198      200       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvY29uc3Rfc3RyaW5nLmhwcA==) | `98.26% <98.13%> (-1.74%)` | :arrow_down: |  
| [include/boost/url/impl/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaHBw) | `98.48% <100.00%> (ø)` | |  
| [include/boost/url/impl/const\_string.ipp](https://codecov.io/gh/CPPAlliance/url/pull/127/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [3dba527...21ce2dc](https://codecov.io/gh/CPPAlliance/url/pull/127?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-02-20 21:27:44 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1046324395  

> But https://godbolt.org/z/8cd31xE74  
  
Constructors work fine and are tested here:  
  
https://github.com/alandefreitas/url/blob/21ce2dc6cbb154bfc16a28cd77d90f6aca87d2d9/test/unit/const_string.cpp#L423  
  
Copy initialization (correctly) fails for _both_ `operator string_view` and the `string_view` base class though:  
  
https://godbolt.org/z/11hdEGb5f  
  
I've updated the description with the use cases:  
  
> The tests ensure all cases of ambiguity discussed on January 20th have been fixed:  
> - Use const_string in functions expecting std::string: https://godbolt.org/z/ahbf3P85h / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L393)  
> - Use const_string to copy initialize std::string: https://godbolt.org/z/a9rjq716K / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L403)  
> - Use const_string to construct std::string: https://godbolt.org/z/3jPeaP67e / https://godbolt.org/z/9hEa38hxh / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L400)  
> - Use const_string in functions expecting std::string_view: https://godbolt.org/z/a8KPj8G5K / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L387)  
> - Use const_string to construct classes analogous to boost::json::string that are construtible from string_view, as requested on January 20th but that didn't work before this PR: https://godbolt.org/z/a8KPj8G5K / https://godbolt.org/z/13nxWKdM4 / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L416) / [ref](https://github.com/alandefreitas/url/blob/01de8fa42044ad04aa11b8048f782e077ae54ab6/test/unit/const_string.cpp#L431)  
  
and included a comment summarizing the situation with copy initialization  
  
> About copy-initialization:  
> - Objects that (i) are not `std::string_view`, `core::string_view` or `std::basic_string`; and (ii) are implicitly constructible from `string_view` or `std::string_view` cannot be copy initialized from `const_string`.   
> - This is not a problem for constructors and functions that expect implicit conversions.  
> - This problem happens for both `operator string_view` and for `string_view` as a base class, unless `const_string` would inherit from both `string_view` _and_ `std::string_view` just to make this work.  
> - The [reason](https://en.cppreference.com/w/cpp/language/copy_initialization#Notes) is "the implicit conversion in copy-initialization must produce T directly from the initializer, while, e.g. direct-initialization expects an implicit conversion from the initializer to an argument of T's constructor"  
> - The only solution would be an implicit conversion from `const_string` to any type `T` constructible from `string_view`, which leads to a lot of ambiguity.   
>     - Some newer compilers prefer the overload closest to the copy constructor and don't fail.   
>     - Most compilers consider that ambiguous with all other constructors from types from where we can create a string_view.   
>     - For instance, `ipv6_address(ipv6_address const&)`, `ipv6_address(ipv4_address const&)`, and `ipv6_address(string_view)` would be ambiguous because we can convert a const_string to any of them.  
>     - We _could_ write workarounds to isolate and support the compilers where this works. It doesn't seem like it's worth leading the user in this direction.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-02-20 21:57:43 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1046329047  

@pdimov ?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-02-20 22:23:33 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1046333790  

I just realized that the list regarding "copy initialization" is really just one thing not five

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-03-11 22:49:58 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1065613360  

As requested, I've updated the description with tony tables, but I believe we should close this PR.   
  
It seems like I miscalculated how important this is and we can always use the closed PR as a reference if anyone ever asks about this again.

---

## Comment 7

> Username: akrzemi1  
> Created_at: 2022-07-04 15:24:26 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1173936389  

> * Requires workaround for a few older compilers (somewhere around GCC >5.0 <= 6.0) that provide initial support for `std::string_view` but consider its conversions ambiguous (see [ref](https://github.com/alandefreitas/url/blob/21ce2dc6cbb154bfc16a28cd77d90f6aca87d2d9/include/boost/url/detail/config.hpp#L96))  
  
Can this be demonstrated in Compiler Explorer?

---

## Comment 8

> Username: alandefreitas  
> Created_at: 2022-07-04 15:59:07 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1173964314  

> Can this be demonstrated in Compiler Explorer?  
  
Yes. I don't remember which conversions caused problems on which specific compilers because it's been a while. But yes. It was already demonstrated in CI so it would just be a matter of replicating [this function](https://github.com/alandefreitas/url/blob/21ce2dc6cbb154bfc16a28cd77d90f6aca87d2d9/test/unit/const_string.cpp#L321) in Compiler Explorer.  
  
Maybe the condition  
  
```cpp  
#if defined(BOOST_NO_CXX17_HDR_STRING_VIEW) || \  
    BOOST_WORKAROUND( BOOST_GCC_VERSION, < 60000 ) || \  
    BOOST_WORKAROUND( BOOST_CLANG_VERSION, < 60000 ) || \  
    (defined(__MINGW32__) && BOOST_WORKAROUND( BOOST_GCC_VERSION, < 90000 ))  
#define BOOST_URL_NO_CXX17_HDR_STRING_VIEW  
#endif  
```  
  
can be adjusted to avoid more false positives. Or the conversion operators can be adjusted to work at least partially on these compilers since the problem was having two ambiguous conversions and we could have at least one of them.  
  
To be honest, IIRC, I think we eliminated this alternative mostly because of the extra code, complexity, and the repetition of patterns from `string_view`. So we didn't really explore any alternatives to improve the workaround.

---

## Comment 9

> Username: akrzemi1  
> Created_at: 2022-07-04 17:09:53 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1174013398  

So basically this sounds like a trade-off between the library authors having to write definitions of funcitons already present in `string_view` on the one hand, and offering users a potential UB and violating the Liskov substitutability principle on the other.

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2022-07-04 17:25:42 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1174022595  

> trade-off between the library authors having to write definitions of functions already present in string_view on the one hand, and offering users a potential UB and violating the Liskov substitutability principle on the other  
  
Yes. That sounds correct. But I wrote this PR so I'm biased. There are probably other complaints.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-07-05 13:54:34 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1175089760  

@akrzemi1  I'm not terribly concerned about Liskov's substitution principle. But if you think you have a better solution then I urge you to reveal it to us, in the form of sample code. Because I know that `const_string` has its problems. It seems to be the least worst though.

---

## Comment 12

> Username: akrzemi1  
> Created_at: 2022-07-05 15:44:40 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1175210776  

The solution I have in mind is the one represented by this PR: keep `const_string` but add conversion operators rather than inheritance.

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2022-07-05 16:03:19 UTC  
> Url: https://github.com/boostorg/url/pull/127#issuecomment-1175229206  

hmm..... one thing is certain, @alandefreitas does good work :)  
  
I'm open to reconsidering these design choices, especially considering that Boost.HTTP.Proto is using `const_string` as a building block:   
https://github.com/CPPAlliance/http_proto/blob/7ff581a3d68d56a02e1936b6a3efc474836d4f56/include/boost/http_proto/fields_view_base.hpp#L295

---
