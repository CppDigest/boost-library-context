# #522 Remove MPL from e_float [Closed]

> Username: mborland  
> Created at: 2021-02-07 11:17:13 UTC  
> Updated at: 2021-02-15 17:31:00 UTC  
> Closed at: 2021-02-15 13:12:02 UTC  
> Url: https://github.com/boostorg/math/pull/522  

Removes use of MPL from e_float. Also replaces: C style casts, C style arrays, and others with C++11 isms.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-07 12:33:54 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-774667145  

I suspect this file is seriously under-tested at present - @ckormanyos as this was the predecessor to Boost.Multiprecision how would you feel if we deprecated this binding?

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-02-07 13:29:05 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-774674662  

> I suspect this file is seriously under-tested at present  
  
Correct.  
  
> as this was the predecessor to Boost.Multiprecision how would you feel if we deprecated this binding?  
  
That would be alright. Unless we revitalize both the binding as well as the underlying C++ code and also correct it and properly test it within the C++11 environment, the legacy `e_float` wrapper has served its original purpose and can retire.  
  
There is something, however, that _could_ be pursued in its place. As a new, well-documented example, I could imagine that we might like to show how to wrap an existing, self-written, non-boost, floating-point backend into `boost::multiprecision::number` and subsequently use it effectively with selected functions from Boost.Math. I have often been highly impressed by the backend requirements of `number` and used these with great success in non-published projects. To this end, my original research in `e_float` has (in 2020) now seamlessly melted into a new template-only C++11/14/17/20 project that might serve as a replacement for how-to-show-a-float-number-binding in Boost if we decide to pursue this kind of thing in an example. The nicer things about the newer child of `e_float` is that it is C++11/14/17/20-compatible and header only and also in Git ready to pull. So it could harmonize nicely as a coherent, future-ready, supported (correct and tested) example of wrapping a custom backend.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-02-07 14:01:41 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-774679249  

I can add `BOOST_HEADER_DEPRECATED`. Is there a specific header in multi-precision that I should have it point at?

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-02-07 16:10:52 UTC  
> Updated_at: 2021-02-07 16:11:56 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-774700453  

> I can add `BOOST_HEADER_DEPRECATED`.  
  
Good idea. Then we can defer its removal.  
Thank you, Matt.  
  
> Is there a specific header in multi-precision that I should have it point at?  
  
No. There is no replacement at the moment.  
  
I will make a mental note to see if a few simple operations compile and link with my local copy of `e_float` nonetheless. After that, we can re-convene in order to decidedly figure out what we would like to do with the `e_float` bindings.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-02-07 16:31:24 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-774703851  

Guys, I just dusted off `e_float` 2021 and the thing kind of psyched me up. It's clean, error/warning-free, on VS2019, C++11-ready. (See pic below).  
  
Kind of my inspiration would go far...  
If you grant me time till next release, i would like to try to:  
- Clean up `e_float` and its wrapper in Boost  
- Publish `e_float` 2021 as an (allowed) derivative work on GitHub.  
- Get it to build, link, run its functional tests in GHA standalone.  
- Hook it up to the newly cleaned Boost wrapper, integrate it in a few basic tests via pull in GHA and build via MSbuild.  
  
Thoughts?  
  
![e_float](https://user-images.githubusercontent.com/2404721/107152812-4af5ba80-696a-11eb-8fd6-6a20bc36e655.JPG)

---

## Comment 6

> Username: mborland  
> Created_at: 2021-02-07 18:35:46 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-774728941  

@ckormanyos I have no issue with closing this PR if you are going to improve upon what is already here. I am just trying to strip out the uses of MPL, and refactor to C++11 when I am already making changes.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-02-08 10:14:08 UTC  
> Updated_at: 2021-02-08 10:17:44 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-775032871  

> I have no issue with closing this PR if you are going to improve upon what is already here  
  
Yes. Please close the PR as you see fit. Any movement (in so far as we decide to make it) on the `e_float` backend wrap for multiprecision can easily be handled in new PR(s) separately from this PR.  
  
Along those lines, I did in fact, contribute the [historical legacy `e_float-2021` repo](https://github.com/ckormanyos/e_float-2021). It builds the _efx_ backend on MSVC 14.2 x64 in GHA. Evidently, I made my own Boost wrapper around 2017, but never pursued its publication or verification seriously.  
  
We could:  
- Remove the `e_float`wrapper from boost and simply mention the historical legacy `e_float-2021` repo as a kind of origin of the work.  
- Remove the `e_float` wrapper from boost, but refresh it, correct it and have it within `e_float-2021`.  
- Revitalize the `e_float` wrapper in boost and build `e_float` via GHA in Boost tests with its wrapper.  
  
I'm OK with any of the above choices.  
  
Thoughts?

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2021-02-09 15:39:25 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776031299  

OK [this e_float binding](https://github.com/ckormanyos/e_float-2021/blob/main/boost/math/bindings/e_float.hpp) in my local repository is a worthy start on a predecessor to the original e_float bindings. The new file harmonizes with `e_float-2021` and is also C++11,14,17,20 compatible (at least headed that way). Some rudimentary tests with math have been done locally.  
  
Thoughts on whether or not I should pursue this?

---

## Comment 9

> Username: mborland  
> Created_at: 2021-02-09 17:09:38 UTC  
> Updated_at: 2021-02-09 17:09:48 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776093998  

@ckormanyos If you have that local `e_float` binding I think that nests well with your third option above which was:  
>Revitalize the e_float wrapper in boost and build e_float via GHA in Boost tests with its wrapper.  
  
To me it makes sense to revitalize/upgrade the wrapper that is here as opposed to deprecating this, and placing the successor in a different repo.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-02-09 17:38:43 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776113451  

>> Revitalize the e_float wrapper in boost  
  
> To me it makes sense to revitalize/upgrade the wrapper that is here  
  
I have begun doing this and will report on progress/get advice as needed as this development activity goes on.

---

## Comment 11

> Username: mborland  
> Created_at: 2021-02-09 17:40:41 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776114764  

> I have begun doing this and will report on progress/get advice as needed as this development activity goes on.  
  
Sounds good. I will close this then.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-02-09 17:43:18 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776116458  

>> I have begun doing this and will report on progress/get advice as needed...  
  
> Sounds good. I will close this then.  
  
Great. I did do a checkin on your branch. Should I remain on your branch and fire up another PR for this next re-vitalization of `e_float` wrapper activity?

---

## Comment 13

> Username: mborland  
> Created_at: 2021-02-09 18:05:30 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776131207  

If you want to use my branch I can reopen and rename.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-02-09 19:33:08 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-776187604  

> If you want to use my branch I can reopen and rename.  
  
Yes. Understood. I am on your branch and comfortable there.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2021-02-13 16:29:14 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-778641409  

> Understood. I am on your branch and comfortable there.  
  
Hi @mborland I decided to go onto a new branch in math for this issue. Thank you for your help and sorry about the back and forth.  
  
Also, I am a bit weak on the new refactorings and the like you are doing removing all sorts of mpl. I think my new [`e_float.hpp` header](https://github.com/boostorg/math/blob/e_float_2021/include/boost/math/bindings/e_float.hpp) still uses mpl. Do you think you could advise me on how to refactorize these for C++11? Or if you like, please edit if you qwant yourself.

---

## Comment 16

> Username: mborland  
> Created_at: 2021-02-13 16:42:11 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-778643154  

@ckormanyos No issue; I will just roll it back to the original commit. If you open up a new PR with that branch I can advise you on the removal of MPL/general modernization. Since I do not have edit rights to this repo I can not make changes directly.

---

## Comment 17

> Username: mborland  
> Created_at: 2021-02-15 13:12:02 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779214939  

Strictly the removal of MPL from e_float is rolled into PR #540.

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2021-02-15 14:59:03 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779275562  

> Strictly the removal of MPL from e_float is rolled into  
  
Yes. Thank you Matt.  
I was able to comprehend most of that.  
  
I am, however, struggling, wondering if there is a C++-ish replacement for the following?  
  
```  
    typedef boost::mpl::list<  signed long long> signed_types;  
    typedef boost::mpl::list<unsigned long long> unsigned_types;  
    typedef boost::mpl::list<long double>        float_types;  
```

---

## Comment 19

> Username: mborland  
> Created_at: 2021-02-15 15:20:27 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779288937  

A C++11 typelist is as easy as:  
  
```  
template<typename... T>  
struct type_list {};  
```  
  
[Here](http://www.pdimov.com/cpp2/simple_cxx11_metaprogramming.html) is a good read on C++11 template meta-programming. It is the basis for how I am pulling MPL out of the policies.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2021-02-15 15:29:45 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779295114  

> A C++11 typelist...  
  
I suppose I should know this, but for something like `typedef boost::mpl::list<signed long long> signed_types;`, do I need to manually write all the signed integer types that are lass than or as wide as `signed long long` including all those `char_t` types, etc.?  
  
I hope not. Or do we need to build up some other meta-programming helpers?

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2021-02-15 15:34:05 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779297583  

I mean, sure, I can build it up with variadic templates and integral constant stuff from <type_traits>. But that was kind of the whole point of boost::mpl. I wonder if is't so cool to kick that out just because the stuff can be re-invented with C++11-isms, albeit more elegant, easy and reducing strongly the boost-dependence.

---

## Comment 22

> Username: mborland  
> Created_at: 2021-02-15 15:47:13 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779305828  

I will be adding some meta-programming helpers into tools. You could accomplish what you want with insertions into the list like MPL. Almost everything I have changed so far is reducing boost dependencies because C++11 made them unnecessary. [Here is a good reddit thread](https://www.reddit.com/r/cpp/comments/gfowpq/why_you_dont_use_boost/) on why people avoid boost; the monolithic nature of dependencies comes up quite a few times. If nothing else [Boost.MP11](https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html#mp_push_frontl_t) offers MPL functionality without all of the pre-modern workarounds. The code base is many times easier to read.

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2021-02-15 16:43:52 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779338932  

@ckormanyos : the conceptual requirements for multiprecision backends to `number` have changed as a result of removing the MPL dependencies, please use a `std::tuple<>` as a typelist instead of mpl::list.  
  
For a typedef like `signed_types` you must include the widest type available - `std::intmax_t` plus whichever other types the backend interoperates with _natively_.  So if the only integer constructor is for `std::intmax_t`, then that's the only type in the list, but if there are constructors for from say int, and long as well, but not short, then the list would be `std::tuple<int, long, `std::intmax_t`>`.

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2021-02-15 17:31:00 UTC  
> Url: https://github.com/boostorg/math/pull/522#issuecomment-779364005  

> the conceptual requirements for multiprecision backends to `number` have changed as a result of removing the MPL dependencies, please use a std::tuple<> as a typelist instead of mpl::list.  
  
Oh yes. That is great advice! Thank you  @jzmaddock.  
  
Just as i was finishing my stroll I realized that we must establish a method _and_ change the docs. As usual, you have already handled this before i even get around to realizing it.  
  
Thank you very much, guys.

---
