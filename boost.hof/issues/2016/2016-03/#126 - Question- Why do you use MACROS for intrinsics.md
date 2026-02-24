# #126 - Question: Why do you use MACROS for intrinsics? [Closed]

> Username: viboes  
> Created at: 2016-03-06 01:42:51 UTC  
> Updated at: 2016-03-11 17:01:57 UTC  
> Closed at: 2016-03-06 23:20:02 UTC  
> Url: https://github.com/boostorg/hof/issues/126  

I suspect that not all the compiler you support don't provides provide those traits. Which one doesn't support those traits?  
  
I know that this is an implementation detail, but why do you use macros as  
  
```  
#define BOOST_FIT_IS_EMPTY(...) __is_empty(__VA_ARGS__)  
```  
  
or  
  
```  
#define BOOST_FIT_IS_EMPTY(...) std::is_empty<__VA_ARGS__>::value  
```  
  
instead of using a trait  
  
```  
template <class T>   
struct is_empty {  
    static const bool value = __is_empty(T)  
};  
```  
  
It is in order to avoid the `::value`?  
  
Those macros are distracting the reading of the code. Could you consider to replace the macros?  
  
BTW, this should belong to the type_traits library, isn't it?

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-06 02:01:59 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192780471  

This is to avoid the extra template instantaitions. Something similiar is done in Boost.Hana, it probably should be refactored to a separate library that both of them uses in the future.

---

## Comment 2

> Username: viboes  
> Created at: 2016-03-06 10:10:02 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192866254  

Louis, could you comment?

---

## Comment 3

> Username: ldionne  
> Created at: 2016-03-06 14:44:34 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192901553  

I think I just did not think about making them structs with `#ifdef`s around them instead of macros. But as Paul says, it makes a lot of sense to use the intrinsics directly to reduce the number of instantiations, since these are used in very critical places (at least in Hana).

---

## Comment 4

> Username: viboes  
> Created at: 2016-03-06 15:07:08 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192906496  

I we have troubles with the time spent by these instantiations, that mean that we have missed something in the standard.  
  
Both of you know better than me the compilation time issues. If this is the way to do it we will need something like that in Boost.TypeTraits for the traits needing compiler support.  
  
What do you think?

---

## Comment 5

> Username: ldionne  
> Created at: 2016-03-06 15:09:38 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192908862  

I think that Boost.TypeTraits could benefit from using compiler intrinsics when available (I don't know if it does right now), but I think exposing these intrinsics as macros to the user would be insane. It's just an implementation detail in Fit and Hana, that's all. Also, perhaps the gain from using a macro vs a struct that uses the intrinsic is not much, I don't remember benchmarking it.

---

## Comment 6

> Username: viboes  
> Created at: 2016-03-06 15:23:26 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192913741  

Maybe this should go to TypeTraits/Detail so that other Boost libraries can use them without needed to redefine them.

---

## Comment 7

> Username: viboes  
> Created at: 2016-03-06 15:24:19 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192913799  

Paul, Louis, do you mind doing some benchmarks?

---

## Comment 8

> Username: ldionne  
> Created at: 2016-03-06 17:25:18 UTC  
> Updated at: 2016-03-06 17:26:52 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192939009  

Normally, the standard type traits in `<type_traits>` should use intrinsics whenever available, and users  have modest requirements on the compile-time performance of type traits anyway. So basically, I'd advise to just naively use the `std` type traits and be done with it. In Hana, however, things are slightly different because these type traits are used in performance-critical places. Hence, it's worth using intrinsics as a peephole optimization, even though it's a small one:  
  
![screen shot 2016-03-06 at 12 22 52](https://cloud.githubusercontent.com/assets/700834/13555728/3707444e-e396-11e5-88ab-75b3a8400928.png)  
  
Also, and perhaps most importantly, this allows intrinsics to be used on Clang even when the standard library would not use them, e.g. when using Clang with libstdc++ or Clang-cl on Windows. For most users, this shouldn't make a huge difference, but taking a noticeable compile-time hit in Hana just because the underlying `<type_traits>` implementation changes is unacceptable.  
  
**Edit**: The above benchmark shows the difference between using libc++'s `<type_traits>` (which use intrinsics internally), vs using intrinsics directly using macros. It does not show the difference between using intrinsics and `<type_traits>` implemented without intrinsics, cause I only have access to libc++.

---

## Comment 9

> Username: viboes  
> Created at: 2016-03-06 17:52:37 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192946280  

Thanks Louis for these quick results. I would say that if TypeTraits make use of the intrinsics when the standard library don't, we should get good performances while using Boost.TypeTraits.  
  
I can understand that you need some extra performances and that the direct use of the intrinsics via the macros give this improvement, minor, but needed.   
  
Even if there are not too many of such macros, I would like to have them in a common sub-library library. I hate to have the same functionality twice.   
  
Boost.TypeTraits/Intrinsic or Boost.Detail/Intrinsic could be good candidates. Evidently this should be discussed on the Boost development  ML.  
  
I believe that the subject is not important enough to raise it now during the review as there are things that are more important, so I propose to label this issue as Boost-Enhancement.  
  
Paul, I would like to be able to add labels, and I can not due to admin rights. Could you give me temporary rights (my Github user's name is viboes).

---

## Comment 10

> Username: ldionne  
> Created at: 2016-03-06 17:56:40 UTC  
> Updated at: 2016-03-06 17:57:03 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192947170  

If you want to add the intrinsics to Boost.TypeTraits, I don't mind. But Hana will definitely not use these, as it's required to work outside of Boost, and the sheer `#include` of Boost.TypeTraits would likely be an unacceptable performance hit for Hana. But if you think this is an useful addition to Boost.TypeTraits, then by all means go for it (it's very simple to do, see [`boost/hana/detail/intrinsics.hpp`](https://github.com/boostorg/hana/blob/master/include/boost/hana/detail/intrinsics.hpp)).

---

## Comment 11

> Username: pfultz2  
> Created at: 2016-03-06 19:42:12 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-192971904  

Boost.TypeTraits most likely will not use intrinsics since it wants consistent behavior across all platforms. There is some limitations with the emulation, and they don't want to have the type traits work on one platform and not the other.   
  
However, I do think it would be good to have a small core metaprogramming library that contains these things that could be used by Hana and other boost libraries, but this could be looked at later when more libraries start using these constructs.

---

## Comment 12

> Username: viboes  
> Created at: 2016-03-06 21:39:59 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193002046  

Well as both want to maintain your intrinsic macros, I don't see the need for something common. I believed that we were talking of Boost Libraries, but it seems you prefer to be completely decouple from  Boost, even if some things are duplicated.  
  
Louis, do you use Boost.Config or Boost.Predef?

---

## Comment 13

> Username: viboes  
> Created at: 2016-03-06 21:51:04 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193003380  

I have checked it and not you don't use, but your case is particular as you support only the last compilers C++14 compliant. This is a good option that makes the code clear.

---

## Comment 14

> Username: ldionne  
> Created at: 2016-03-06 21:51:56 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193003444  

> Louis, do you use Boost.Config or Boost.Predef?  
  
I don't. Don't get me wrong, I'm not against the usage of Boost libraries to implement other Boost libraries (that would be unwise IMO). However, for some very simple needs, it can be overkill to pull Boost as a dependency. I feel like it would be overkill to require the rest of Boost as a dependency for just a single file containing simple macros.  
  
Another thing. Hana has made the step of refusing to cooperate with non-compliant compilers. A lot of code in _Boost.Whatever_ is used to make _Whatever_ portable across compilers, some of which may be very old. Given the compiler requirements of Hana, there is little benefit to pull a lot of code for the sake of portability.  
  
If there was such a thing as a `Boost.ModernConfig` library that  
1. Was a single header file  
2. Was only concerned with modern compilers  
3. Did not require any other library to work  
  
then I'd be happy to copy/paste it into Hana's tree. But attaching Hana to a specific version of Boost for this simple functionality is not something I'd want.

---

## Comment 15

> Username: viboes  
> Created at: 2016-03-06 22:26:36 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193008383  

No problem. Nevertheless, we need to address this concern, otherwise we will not have a Configuration  file for C++14 only libraries. Would you accept to depend on a sub-library (adding a sub-module somewhere) without doing a copy/paste?  
  
Maybe it is time to think of Boost 2.0.0 as a set of modules(libraries, components) that state explicitly the dependencies on other modules.

---

## Comment 16

> Username: ldionne  
> Created at: 2016-03-06 22:54:48 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193011388  

> Would you accept to depend on a sub-library (adding a sub-module somewhere) without doing a copy/paste?  
  
IDK. Given the current mess when it comes to reusing C++ code, I can't say for sure whether I would. It's less for me than for potential users that have to install the library. All I know is that it's a major putoff for me when I see a library with external dependencies, because I'm always like "oh god what am I getting into?". My dream (until we get a proper way to use other people's code in C++) would be a single header that I can copy/paste into my own library, no strings attached. Then, I would only have to update this single header to match its latest version on important releases of Hana.  
  
This is a stupid thing to do, but I feel like this would be my best option given the lack of a proper way to manage dependencies. And this is true for Hana, which has very modest needs, but not necessarily for other projects that benefit more from external dependencies.  
  
> Maybe it is time to think of Boost 2.0.0 as a set of modules(libraries, components) that state explicitly the dependencies on other modules.  
  
This. My opinion is that we need to move away from the monolithic Boost monster. Not that we can't keep shipping a monolithic package as we do currently (which is useful for several reasons), but individual libraries should IMO be usable on their own too, provided their dependencies are satisfied. With this vision, explicitly stating the dependencies of each library is the only way to go.  
  
I feel like we're slowly drifting towards a discussion about modules and package management. I'm not qualified to have this discussion on my own, and this is something that should be brought up on the Boost mailing list or another C++ list.

---

## Comment 17

> Username: viboes  
> Created at: 2016-03-06 23:20:02 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193014743  

Closed as we don't expect any issue to this question.

---

## Comment 18

> Username: pfultz2  
> Created at: 2016-03-07 05:18:58 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193106968  

> If there was such a thing as a Boost.ModernConfig library that  
>    Did not require any other library to work  
  
Boost.Config has dependencies?

---

## Comment 19

> Username: pfultz2  
> Created at: 2016-03-07 05:31:31 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193110594  

> IDK. Given the current mess when it comes to reusing C++ code, I can't say for sure whether I would. It's less for me than for potential users that have to install the library. All I know is that it's a major putoff for me when I see a library with external dependencies, because I'm always like "oh god what am I getting into?". My dream (until we get a proper way to use other people's code in C++) would be a single header that I can copy/paste into my own library, no strings attached. Then, I would only have to update this single header to match its latest version on important releases of Hana.  
  
I understand where you are coming from, but I think its the wrong direction to go. I've started to write a tool that will fetch, build, and install cmake libraries, called [cget](https://github.com/pfultz2/cget). Its still a WIP, but you can already do `cget install boostorg/hana` to get the latest hana version installed, or `cget install boostorg/hana@v0.7.0` to get version 0.7.0. I am currently working on having it automatically install dependencies by reading a requirements.txt file as well. I plan in the future to add channels which will have better support for versions and checksums. We can discuss this tool more on cget's page, if you like. I think a tool like that will help manage the dependency problem with C++.  
  
> This. My opinion is that we need to move away from the monolithic Boost monster. Not that we can't keep shipping a monolithic package as we do currently (which is useful for several reasons), but individual libraries should IMO be usable on their own too, provided their dependencies are satisfied. With this vision, explicitly stating the dependencies of each library is the only way to go.  
  
Ideally, it would be nice to at least get the core libraries to support cmake and pkg-config. This way libraries can start transferring to a modern build and packaging system, and it doesn't need to be a big jump.

---

## Comment 20

> Username: viboes  
> Created at: 2016-03-07 06:42:26 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-193124446  

No Boost.Config and Boost.Predef have no dependencies. Boost.Core depends on Boost.Config and Boost.Assert.    
  
I don't know if this is the last report http://www.pdimov.com/tmp/report-develop-c3bb6eb/module-weights.html.

---

## Comment 21

> Username: pfultz2  
> Created at: 2016-03-11 17:01:57 UTC  
> Url: https://github.com/boostorg/hof/issues/126#issuecomment-195454803  

That report just measure libraries used by the headers, and it doesn't show the dependencies used by the tests.
