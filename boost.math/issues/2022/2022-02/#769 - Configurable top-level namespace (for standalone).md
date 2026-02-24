# #769 - Configurable top-level namespace (for standalone) [Open]

> Username: CaseyCarter  
> Created at: 2022-02-28 23:10:08 UTC  
> Updated at: 2024-08-24 18:13:47 UTC  
> Url: https://github.com/boostorg/math/issues/769  

As you probably already know, MSVC uses Boost Math to implement the C++17 mathematical special functions. (My gratitude and joy over this fact cannot be expressed with words - I did _not_ want to spend a year of my life becoming expert enough in numerical analysis to implement these properly myself!) We have a thin wrapper layer that adapts the Standard's API to Boost's, which works great when folks link to the STL dynamically. We do have an issue, however, in that symbols in namespace `::boost` are directly visible in our static libraries, causing ODR problems for folks who are using different versions of Boost Math than we ship.  
  
Would you be amenable to a PR that made the top-level namespace configurable if we were to implement such a feature? We could maintain a fork, of course, but (1) it seems like this might be useful for other folks that want to ship third-party libs using Boost Math, and (2) we like to keep the STL's list of dependencies short and clean to make it easier to onboard new contributors.

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-02-28 23:21:06 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1054805746  

@jzmaddock will need to sign off, but a priori I see no reason why this shouldn't be done.  
  
Have you tried the standalone? Might help to scope the problem a bit and I believe there is CMake tooling to help with this particular problem.

---

## Comment 2

> Username: StephanTLavavej  
> Created at: 2022-03-01 00:47:29 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1054859192  

> Have you tried the standalone?  
  
Yep! We've been using it since we merged https://github.com/microsoft/STL/pull/2151 in Aug 2021.

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-03-01 00:51:05 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1054860939  

Could this be done via `set_target_properties(boost_math PROPERTIES CXX_VISIBILITY_PRESET hidden)`? You'll have to excuse my naivety if this is totally underestimates the effort; I could never figure out what symbol visibility is . . .

---

## Comment 4

> Username: StephanTLavavej  
> Created at: 2022-03-01 06:55:23 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1055087049  

According to my extremely vague understanding, that's already the default for MSVC, and it applies to DLLs. On the MSVC platform, we don't usually have a term for it, since one has to intentionally "dllexport" a symbol (otherwise it's what other platforms would call "hidden"). But within a static library, the concept of "dllexported vs. hidden" doesn't apply - all symbols are mixed together with no isolation.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-03-01 08:39:57 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1055161567  

Seems like a perfectly reasonable request, I guess we would just have to macro-ise the namespace open and closing?

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-03-01 09:38:07 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1055219051  

> perfectly reasonable request, I guess we would just have to macro-ise the namespace open and closing?  
  
Yes. I have done something like this in other projects. Basically, you provide a macro which can supply an outer namespace (which can also be a C++17 nested namespace).  
  
You then need to define separate `_BEGIN` and `_END` macros.  
  
The big change I found, however, was in testing. Since you will also want to test some tests with/without the outer namespace, you might end up needing to change a lot of test code to either be `using` the outer namespace(s) or fully supply the outer namespace resolution. Perhaps my experience was wrong or somehow incomplete, but I found the big effort in re-tooling the mountain of test code.

---

## Comment 7

> Username: CaseyCarter  
> Created at: 2022-03-01 17:52:36 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1055701484  

> Seems like a perfectly reasonable request, I guess we would just have to macro-ise the namespace open and closing?  
  
Yes, that's what I had in mind. My precise use case could be addressed by using a macro for the top-level namespace name (`namespace BOOST_MATH_TOP_LEVEL { /* ... */ }`) but more generally I can imagine folks wanting to nest within an arbitrary namespace. This is best achieved with "open math namespace from the global namespace", "close math namespace", and "fully-qualified name of math namespace" macros. (The latter is necessary if you need to specialize templates in another namespace - say `std` - for math types. I'm not sure if Math does this today, but it probably will eventually.)  
  
One design choice to be made is whether the open/close macros include the inner-most braces or not. In other words, do you want:  
```c++  
BOOST_MATH_BEGIN // default definition "namespace boost {"  
template <class T> class potato;  
BOOST_MATH_END // default definition "}"  
namespace std {  
template <class T>  
struct numeric_limits<BOOST_MATH_PATH::potato<T>> { /* ... */ }; // default definition "::boost"  
}  
```  
or  
```c++  
BOOST_MATH_BEGIN { // default definition "namespace boost"  
    template <class T> class potato;  
} BOOST_MATH_END // default definition ""  
namespace std {  
    template <class T>  
    struct numeric_limits<BOOST_MATH_PATH::potato<T>> { /* ... */ }; // default definition "::boost"  
}  
```  
I believe the former is more common, but the latter is nice for those of us who like to format with indents in namespaces =)

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-03-01 18:28:47 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-1055733409  

I would tend to weakly prefer the latter.

---

## Comment 9

> Username: ckormanyos  
> Created at: 2024-07-07 08:21:13 UTC  
> Updated at: 2024-07-07 08:23:22 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212367229  

Hi @jzmaddock I would like to start giving this one a try. I can maybe handle it.  
  
But we need to decide a few things.  
  
- Macros: I would prefer `BOOST_MATH_NAMESPACE_BEGIN` and `BOOST_MATH_NAMESPACE_END`.  
- Is the idea here to simply create a user-configurable outer namespace such as with: `#define BOOST_MATH_OUTER_NAMESPACE=my_stl_specfun`, whereby the default of this definition is undefined?  
- How much of `::boost::math` should be in the configurable naespace? All of it each and every single file (I suppose so), or only specfun?  
  
This is how I have done this kind of think in the past for some of my own header-only libraries.  
  
So then if the client defines `BOOST_MATH_OUTER_NAMESPACE` with a top-level line such as:  
  
```cpp  
#define BOOST_MATH_OUTER_NAMESPACE=my_stl_specfun  
```  
  
then the _real_ namespace for `::boost::math` will become `::my_stl_specfun::boost::math`. Or is a different mechanism in planing.  
  
Cc: @CaseyCarter and @mborland and @NAThompson

---

## Comment 10

> Username: jzmaddock  
> Created at: 2024-07-07 08:53:48 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212375291  

I had assumed (perhaps incorrectly) that we would maybe use something like:  
  
```  
#define BOOST_MATH_NAMESPACE_START namespace boost{ namespace math{  
#define BOOST_MATH_NAMESPACE_END }}  
```

---

## Comment 11

> Username: ckormanyos  
> Created at: 2024-07-07 08:59:12 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212376829  

> I had assumed (perhaps incorrectly) that we would maybe use something like:  
  
You would have to only define thos if they were undefined, thus giving the client a chance to define them upstream? I don't really like it because then the entire identity of `::boost::math` gets somehow, ... _lost_. I would actually prefer a client-configurable prefix or pre-pended namespace so at least you always know of the Boost origin/legacy.

---

## Comment 12

> Username: ckormanyos  
> Created at: 2024-07-07 09:02:39 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212377978  

But (thinking out loud) would a prepended namespace give the client the desired isolation?

---

## Comment 13

> Username: ckormanyos  
> Created at: 2024-07-07 09:07:30 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212379531  

> would a prepended namespace give the client the desired isolation?  
  
I think it would isolate beacuse you're basically saying, OK `::boost` is a popular, widely-used namespace, let's make sure that a particular standalone specialization of math can be put into `::whatever::boost`

---

## Comment 14

> Username: ckormanyos  
> Created at: 2024-07-07 09:34:56 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212387091  

Do we need to do anything particular in order to ensure that Multiprecision functions can still be found via ADL?

---

## Comment 15

> Username: jzmaddock  
> Created at: 2024-07-07 09:59:48 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2212393171  

> But (thinking out loud) would a prepended namespace give the client the desired isolation?  
  
I assume that anything that changes the name mangling would work equally well.  
  
> Do we need to do anything particular in order to ensure that Multiprecision functions can still be found via ADL?  
  
As long as the namespace is boost::math in the normal case I don't see we need to worry about this?

---

## Comment 16

> Username: frederick-vs-ja  
> Created at: 2024-08-24 11:34:33 UTC  
> Updated at: 2024-08-24 11:35:44 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2308362634  

Will it be sufficient to just replace the `math` part?  
  
----  
I'm trying to resolve this in Boost.Math by adding the `BOOST_MATH_NAMESPACE` macro  
```C++  
#ifndef BOOST_MATH_NAMESPACE  
#define BOOST_MATH_NAMESPACE math  
#endif  
```  
and then replace every occurrence of `math` which is a namespace with `BOOST_MATH_NAMESPACE`.  
  
With the changes above, MSVC STL's [`special_math.cpp`](https://github.com/microsoft/STL/blob/62205ab155d093e71dd9588a78f02c5396c3c14b/stl/src/special_math.cpp) can define `BOOST_MATH_NAMESPACE` as `_Math` (or some other _Uglified identifier, e.g. `__math__`) before including `<boost/math/*>` and then use `::boost::BOOST_MATH_NAMESPACE::*` to get rid of non-_Uglified symbols.  
  
But the changes need to touch 500+ files in Boost.Math and make maintenance a bit harder (every namespace `math` should be spelt as `BOOST_MATH_NAMESPACE` later). Not sure whether this approach is OK.

---

## Comment 17

> Username: CaseyCarter  
> Created at: 2024-08-24 16:17:47 UTC  
> Updated at: 2024-08-24 16:18:14 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2308445056  

> Will it be sufficient to just replace the `math` part?  
  
Assuming every symbol defined by the library is currently in `boost::math`, I suppose it might suffice. I'd nonetheless prefer the fully general ability to change the entire namespace rather than just a suffix.  
  
Purely aesthetically, I would rather write `::BOOST_MATH_NAMESPACE::meow(args)` than `::boost::BOOST_MATH_NAMESPACE::meow(args)`, but both are admittedly pretty ugly and I'd probably define another macro for this internal use.

---

## Comment 18

> Username: ckormanyos  
> Created at: 2024-08-24 16:20:28 UTC  
> Updated at: 2024-08-24 16:21:14 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2308445894  

I prefer an _outer_ namespace and have had good results in other projects (non-boost) with that philosophy. Also these other projects heavily use ADL. So I'd go with the outer namespace

---

## Comment 19

> Username: jzmaddock  
> Created at: 2024-08-24 18:13:46 UTC  
> Url: https://github.com/boostorg/math/issues/769#issuecomment-2308480852  

Personally, I would make BOOST_MATH_NAMESPACE equivalent to `boost::math`.  
  
I have no great objection to the changes required, but it does increase the number of configurations we need to test quite a bit.
