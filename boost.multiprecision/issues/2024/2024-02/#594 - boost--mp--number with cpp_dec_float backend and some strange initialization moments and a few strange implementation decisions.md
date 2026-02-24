# #594 - boost::mp::number with cpp_dec_float backend and some strange initialization moments and a few strange implementation decisions [Open]

> Username: niXman  
> Created at: 2024-02-08 20:55:15 UTC  
> Updated at: 2024-02-29 18:52:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594  

hello guys,  
  
firstly: construction/assignments  
let me refer to the [documentation](https://www.boost.org/doc/libs/1_84_0/libs/multiprecision/doc/html/boost_multiprecision/ref/number.html) for `boost::mp::number` class:  
```cpp  
- number();  
- number(see-below);  
- number& operator=(see-below);  
- number& assign(see-below);  
  
  
Type number is default constructible, and both copy constructible and assignable from:  
- Itself.  
- An expression template which is the result of one of the arithmetic operators.  
- Any [fundamental (built-in)](https://en.cppreference.com/w/cpp/language/types) arithmetic type, as long as the result would not be lossy (for example float to integer conversion).  
- Any type that the Backend is implicitly constructible or assignable from.  
- An rvalue reference to another number. Move-semantics are used for construction if the backend also supports rvalue reference construction. In the case of assignment, move semantics are always supported when the argument is an rvalue reference irrespective of the backend.  
- Any type in the same family, as long as no loss of precision is involved. For example from int128_t to int256_t, or cpp_dec_float_50 to cpp_dec_float_100.  
  
Type number is explicitly constructible from:  
- Any type mentioned above.  
- A std::string or any type which is convertible to const char*.  
- Any arithmetic type (including those that would result in lossy conversions).  
- Any type in the same family, including those that result in loss of precision.  
- Any type that the Backend is explicitly constructible from.  
- Any pair of types for which a generic interconversion exists: that is from integer to integer, integer to rational, integer to float, rational to rational, rational to float, or float to float.  
```  
now please look at the [code](https://godbolt.org/z/x3x8E454s):  
```cpp  
#include <string>  
  
#include <boost/multiprecision/number.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <boost/utility/string_view.hpp>  
  
using double_type = boost::multiprecision::number<  
     boost::multiprecision::cpp_dec_float<8>  
    ,boost::multiprecision::et_off  
>;  
  
static const char PI_STR[] = "3.14";  
  
int main() {  
    // ctor  
    {  
        // 0  
        double_type v = PI_STR; // ERROR  
    }  
    {  
        // 1  
        const auto *s = PI_STR;  
        double_type v = s; // ERROR  
    }  
    {  
        // 2  
        boost::string_view s = PI_STR;  
        double_type v = s; // ERROR  
    }  
    {  
        // 3  
        std::string s = PI_STR;  
        double_type v = s; // ERROR  
    }  
  
    // operator=  
    {  
        // 4  
        double_type v;  
        v = PI_STR; // ERROR  
    }  
    {  
        // 5  
        const auto *s = PI_STR;  
        double_type v;  
        v = s; // ERROR  
    }  
    {  
        // 6  
        boost::string_view s = PI_STR;  
        double_type v;  
        v = s; // ERROR  
    }  
    {  
        // 7  
        std::string s = PI_STR;  
        double_type v;  
        v = s; // ERROR  
    }  
  
    // assign  
    {  
        // 8  
        double_type v;  
        v.assign(PI_STR);  
    }  
    {  
        // 9  
        const auto *s = PI_STR;  
        double_type v;  
        v.assign(s);  
    }  
    {  
        // 10  
        boost::string_view s = PI_STR;  
        double_type v;  
        v.assign(s); // ERROR  
    }  
    {  
        // 11  
        std::string s = PI_STR;  
        double_type v;  
        v.assign(s);  
    }  
}  
  
```  
as far as you can see the only way to initialize the `number` class is to use the `number::assign()` MF.  
**so my first question is**: why? is this a documentation mismatch? or is this a consequence of something being broken?  
  
further about some controversial decisions in terms of optimization.  
imagine we end up having to use `number::assign(std::string)`.  
in this case we will follow the path: [number::assign(const std::string &)](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/number.hpp#L390) -> [number::canonical_value(const std::string &)](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/number.hpp#L2236) -> [cpp_dec_float::operator=(const char* v)](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_dec_float.hpp#L401) -> [cpp_dec_float::rd_string(const char* s)](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_dec_float.hpp#L2083).  
  
so, the `cpp_dec_float::rd_string(const char* s)` function is exactly the function that converts a string to the `number`.  
OK, the function takes `const char *` as its argument.  
BUT, it [creates](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_dec_float.hpp#L2090) a `std::string` object which is initialized with the value of the argument!  
that is, we had a `std::string` from which we received `const char *` just to create the `std::string` again!  
(in cases where the source string is longer than the SSO buffer - we will also perform additional allocation and deallocation! I'm not even talking about six calls to `std::string::substr()`)  
  
I briefly looked at the implementation of this function it seems to me that it is quite possible to change the implementation to use `boost::string_view` instead of `std::string`.  
  
but there is another problem with this: based [on this line](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_dec_float.hpp#L2104) - this library is meant to be used as the standalone library, and I don't know if `boost::string_view` is included in the package of the standalone library... (ideas?)  
  
I think if I can change the implementation of that function to use `boost::string_view` instead of `std::string`, then it would make sense to change the signature of that function so it accepts `boost::string_view` instead of `const char *` along with the necessary changes regarding the call that function.  
  
ideas?  
  
  
  
best!

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-02-09 06:57:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935422732  

Thanks for a good post, @niXman. It is good to read your queries.  
  
> ideas?  
  
To be _very_ terse (and I've never addressed John about this), I think long-term migration to a `<charconv>`-like interface with support for `from_chars()`/`to_chars()` would be a good evolution for `number`. But this is quite a ways off at the moment. Subsequently, `number` would dispatch to the read/write functions of the correcsponding backends.  
  
> the `cpp_dec_float::rd_string(const char* s)` function  
  
... is absolutely, definitely, inefficient legacy code. This code is based on work from last century and is in dire need of refactoring. Personally, I wouldn't mess around with `string_view` or anything else, just try to use the raw character string input efficiently (that is, if i ever did refactor it). The backends do, in fact, carry the burden of lexical parsing of input strings. Having said that, however, `cpp_dec_float`'s input character-parser could/should allocate fewer times and maybe be simplified. This has long been on my TODO list.  
  
About the top-level conversion rules WRT docs, I'll punt to John...  
  
Cc: @jzmaddock and @mborland

---

## Comment 2

> Username: mborland  
> Created at: 2024-02-09 07:09:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935433587  

>  
>I briefly looked at the implementation of this function it seems to me that it is quite possible to change the implementation to use boost::string_view instead of std::string.  
> but there is another problem with this: based [on this line](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_dec_float.hpp#L2104) - this library is meant to be used as the standalone library, and I don't know if boost::string_view is included in the package of the standalone library... (ideas?)  
>  
  
`boost::string_view` which comes from Boost.Utility is not one of the primary dependencies of the library. See here for the list of what we do use right now: https://github.com/boostorg/multiprecision/blob/develop/CMakeLists.txt#L28-L36. Since we already have core I would use `boost::core::string_view` since it has the benefit of not adding a dependency, but more importantly it has a conversion operator with `std::string_view` that `boost::string_view` does not. The library does have a standalone component so any boost library component needs to be behind a macro like `#ifndef BOOST_MP_STANDALONE` and documented as such.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-02-09 09:32:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935597646  

+1 on supporting string views.  I wonder if we can conceptualize what makes a string-view type class and just provide a template constructor with no actual dependency?  
  
With regard to construction from string: the class is *explicitly constructible* from string types (ie the constructor is there, but marked as `explicit`).  Your examples reply on *implicit* construction, so:  
  
```  
double_type y{ "3.14" };  
```  
  
Is just fine.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2024-02-09 09:36:06 UTC  
> Updated at: 2024-02-09 09:36:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935601969  

> I wonder if we can conceptualize what makes a string-view type class and just provide a template constructor with no actual dependency?  
  
I'm confused why actually deal with `string_view`s. Not that it's sooo important, but doesn't it basically just boil down to the first and last `char`-pointer? If I recall, `string_view` has no null-termination, or it can't be depended on. Why not juse use something like `from_chars()`?

---

## Comment 5

> Username: mborland  
> Created at: 2024-02-09 09:37:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935604067  

>   
> I'm confused why actually deal with `string_view`s. Not that it's sooo important, but doesn't it basically just boil down to the first and last `char`? If I recall, `string_view` has no null-termination, or it can't be depended on. Why not juse use something like `from_chars()`?  
  
It's pointer to first and size. It's generally equivalent to calling func(str.data(), str.data() + str.size()). It should be an easy concept since it only needs those two members.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2024-02-09 10:48:27 UTC  
> Updated at: 2024-02-09 10:50:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935701624  

>> I'm confused why actually deal with `string_view`s.  
  
> It's pointer to first and size.  
  
Which boils down to first and last. Yes. I am aware.  
  
This is exactly why I'm pressing on with a `from_chars()`-style adaption. Before adding yet more convenience constructors to the (already heavy) backends, I would actually consider creating a uniform, standards-leaning interface right at the top of `number`.  
  
We already know if the underlying type is integral/floating-point (from the backend traits) and if any default radices are needed, we have these from `numeric_limits`.  
  
So clients could create a multiprecision number `from_chars()` regardless of whether they have `std::string`, `string_view` or good ole `char*` (null-terminated or with known length).

---

## Comment 7

> Username: ckormanyos  
> Created at: 2024-02-09 11:07:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935727245  

A second (simple) option which I've used in other projects, retains the `explicit` attribute on the constructors from `char*`. But it adds to these a second parameter `std::size_t` defaulted to `std::numeric_limits<std::size_t>::max()`.  
  
This type of mash-up would be simple to harmonize with the existing code of today. Furthermore, it would allow clients to use `string_view`, `string`, etc. right away.  
  
Independently of this, the inefficiency of `cpp_dec_float`'s construction from `char*` would remain inefficient (until refactored).

---

## Comment 8

> Username: niXman  
> Created at: 2024-02-09 12:29:12 UTC  
> Updated at: 2024-02-09 12:30:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935844199  

@ckormanyos   
  
> Personally, I wouldn't mess around with string_view or anything else, just try to use the raw character string input efficiently  
  
got it, thanks!  
  
@mborland   
  
> Since we already have core I would use boost::core::string_view since it has the benefit of not adding a dependency  
  
oh, great!  
  
@jzmaddock   
  
> the class is explicitly constructible from string types (ie the constructor is there, but marked as explicit).  
  
oh, you are right!  
any thoughts on `operator=(any string type)` ?  
  
@ckormanyos   
  
> A second (simple) option which I've used in other projects, retains the explicit attribute on the constructors from char*. But it adds to these a second parameter std::size_t defaulted to std::numeric_limits<std::size_t>::max().  
  
OK, I will refactor this way.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2024-02-09 12:58:47 UTC  
> Updated at: 2024-02-09 12:59:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1935886794  

> any thoughts on `operator=(any string type)`?  
  
I've heard a few opinions over the years. Often times there is something like this, meaning it gets done. Other developers, however, warn that "your big-number class isn't a string, don't make it behave as one". They say keep such options as explicit as possible.  
  
I kind of agree with the second.

---

## Comment 10

> Username: niXman  
> Created at: 2024-02-12 12:32:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1938594550  

hello, @ckormanyos, @mborland   
  
I think this is my first time I want to make changes to boost, so I have a few questions.  
  
I found this description of the process: https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview  
  
but it looks to me like there are some steps missing.  
1) I think as usual first I should clone `boost` and `multiprecision` subprojects using Github web interface? right? (but in `boost` project I think that `multiprecision` subproject linked as `boostorg/multiprecision` and I dont know what should I do with this)  
2) then I should clone they to my local env?  
3) and after fix I should make PR? right?  
  
or, can anyone just provide me the correct doc for dev process, please?  
  
  
best!

---

## Comment 11

> Username: ckormanyos  
> Created at: 2024-02-12 12:42:05 UTC  
> Updated at: 2024-02-12 12:43:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1938608281  

>  some steps missing.  
  
It's a lot easier than you might think.  
  
Just about the simplest way for Multiprecision can be done more straightforwardly than described in the process for the entire monolithic Boost. We have standalone, so it's super simple to work with either Math or Multiprecision.  
  
- Obtain a copy of the released 1.84.  
- Clone the Multiprecision submodule.  
- Make a branch of the submodule.  
- Develop your code in your branch of the submodule.  
- When ready, submit PR, see how CI goes, and so on.  
- We will help you.  
  
So for your include paths, just put in first the path to the "include" directory of the Multiprecision submodule, then as a second include path, the path to the normal Boost 1.84.  
  
You put the include of the submodule first since that will then get picked up by your compiler _before_ picking up the 1.84 path.  
  
Do your coding that way, push to your branch. You might need to make some new tests. if so, we can cross this bridge when we get to it.

---

## Comment 12

> Username: niXman  
> Created at: 2024-02-12 12:56:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1938630227  

got it, thanks!

---

## Comment 13

> Username: ckormanyos  
> Created at: 2024-02-12 13:23:18 UTC  
> Updated at: 2024-02-12 17:38:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1938673177  

If you encounter difficulties with the setup, just query. It is quite simple so if there are problems, we can work them out.

---

## Comment 14

> Username: niXman  
> Created at: 2024-02-16 13:26:56 UTC  
> Updated at: 2024-02-16 13:27:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1948385168  

guys, I have a question.  
  
please look at this ctor: https://github.com/niXman/multiprecision/blob/develop/include/boost/multiprecision/number.hpp#L119  
and at this one: https://github.com/niXman/multiprecision/blob/develop/include/boost/multiprecision/number.hpp#L126  
  
the question is, since the first one is a constructor - is there any reason to use the `noexcept(noexcept(std::declval<Backend&>() = std::declval<typename detail::canonical<V, Backend>::type const&>()))` assign expr instead of ctor expression, as is done in the second constructor?  
  
ie for noexcept spec: `noexcept(noexcept(Backend(std::declval<typename detail::canonical<V, Backend>::type const&>())))`  
and for backend initialization: `m_backend(canonical_value(v)) {}` instead of `m_backend = canonical_value(v);` ?  
  
  
thanks!

---

## Comment 15

> Username: jzmaddock  
> Created at: 2024-02-16 17:25:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1948945457  

> the question is, since the first one is a constructor - is there any reason to use the noexcept(noexcept(std::declval<Backend&>() = std::declval<typename detail::canonical<V, Backend>::type const&>())) assign expr instead of ctor expression, as is done in the second constructor?  
>  
> ie for noexcept spec: noexcept(noexcept(Backend(std::declval<typename detail::canonical<V, Backend>::type const&>())))  
and for backend initialization: m_backend(canonical_value(v)) {} instead of m_backend = canonical_value(v); ?  
  
Yes:  If you look at what the constructors actually *do*, the second passes the argument to the backend's constructor, while the first default constructs the backend and then assigns to it.  
  
Explanation: it's not a conceptual requirement that the backend is constructable from everything that class `number` is constructable from.  It's acceptable to just provide the assignment operator and have everything pass through that, and class `number` will synthesise what it needs from the operations that the backend *does* support.  These two constructors handle these two cases and are enable_if'ed to be mutually exclusive.  In the line 119 case, the backend is *not* constructible from type V so a noexcept specification that relied on such a conversion wouldn't actually compile anyway.    
  
Hope that somewhat makes sense!  
  
BTW if this is to add support for string_views, I think it's probably a mistake to force backends to natively support them - many backends rely on external C libraries which can handle a `const char*` and nothing else.  
  
So I guess a gold plated solution might be provide 3 constructors:  
  
1) Where the backend is directly constructible from the type, this is probably handled by the line 126 case.  
2) Where the backend is assignable from the string view - the line 119 case.  
3) If neither of the above are true, then a new constructor that converts the character range to a null terminated string and assigns that to the backend.  
  
I would _guess_ case 3 would need to be enable_if'ed on:  
  
* is_string_view (or some similar new trait that identifies string views / character ranges)  
* AND not is_constructible<Backend, V>::value  
* AND not is_assignable<Backend, V>::value  
  
Such a constructor will not be noexcept in any circumstances, because it will presumably need to copy the character range to a new buffer (or a std::string)?

---

## Comment 16

> Username: niXman  
> Created at: 2024-02-17 19:27:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1950289396  

> BTW if this is to add support for string_views, I think it's probably a mistake to force backends to natively support them - many backends rely on external C libraries which can handle a const char* and nothing else.  
  
oh, indeed, it is an incredibly suboptimal `C` interface where either `strlen()` or searching for the last valid character is always called [inside the implementation](https://github.com/alisw/GMP/blob/master/mpf/set_str.c#L189)...  
It’s hard to imagine how many terrawatts of electricity and decades of cpu-time could be saved =)  
  
but yes, eventually we will still have to copy the range of characters into a temporary buffer before calling C backend...  
OK, I have to think...  
  
thank you @jzmaddock !

---

## Comment 17

> Username: jzmaddock  
> Created at: 2024-02-18 16:22:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1951375970  

I suspect that allocating a temporary string is a trivial cost compared to the work that goes into parsing a multiprecision string.  But of course the bitcoin designers probably said much the same thing :-/

---

## Comment 18

> Username: ckormanyos  
> Created at: 2024-02-18 16:27:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1951377129  

Maybe some ideas could be found if you look into the new Boost.Charconv. I think the author was concerned with avoiding extraneous allocation.

---

## Comment 19

> Username: niXman  
> Created at: 2024-02-29 13:28:47 UTC  
> Updated at: 2024-02-29 14:12:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1971142425  

hello guys!  
  
some information.  
  
in order to get rid of all these `strlen()`/`strchr()`/`malloc()`/`memcpy()`/`free()` etc in the backends and in order to unify `backend::operator=()` for strings I need to start with the libraries that are used in the backends , namely: GMP/MPFR/MPFI/MPC  
  
MPFR/MPFI/MPC - depend on GMP, therefore I need to start with this library and its API.  
here I described an example of what needs to be done to get rid of unnecessary `strlen()`/`malloc()`/`memcpy()`/`free()`: https://gmplib.org/list-archives/gmp-discuss/2024-February/006947.html  
  
but, for some reason, no one wants to communicate with me, they are probably afraid to expand the API =)  
(I don’t suggest they change the API)  
(moreover, I offer to do this work for them myself!)  
  
guys, maybe some of you have access to people working with GMP, could you please ping them to advance this issue?  
  
  
best!

---

## Comment 20

> Username: jzmaddock  
> Created at: 2024-02-29 17:00:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1971571900  

I think you have basically zero chance of getting all those disparate libraries to change their API's - remember they're C libraries, have ultra stable ABI's and basically could care less about C++ anyway :(

---

## Comment 21

> Username: niXman  
> Created at: 2024-02-29 17:21:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1971608017  

although I'm not suggesting they change the API, but I'm suggesting they expand it with a new group of functions, I admit that they may simply not be interested in this... :(  
  
tomorrow I will work on the implementation of functionality similar to `mpz_set_str`/`mpf_set_str`/`mpq_set_str`, but without using these functions. perhaps I can achieve the same result but in a different way...

---

## Comment 22

> Username: ckormanyos  
> Created at: 2024-02-29 18:52:16 UTC  
> Updated at: 2024-02-29 18:52:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/594#issuecomment-1971760826  

>> although I'm not suggesting they change the API, but I'm suggesting they expand it with a new group of functions  
  
> I think you have basically zero chance of getting all those disparate libraries to change their API's  
  
Word.  
  
Hi @niXman we do not have a direct line to the authors of these fine low-level backends either. We have done our best possible effort to wrap them based on our empirical observations of _how_ _they_ _work_. And they are well-specified, but we don't meddle with them, and don't aspire to do so.  
  
But at the end of the day, we _wrap_ what we find.
