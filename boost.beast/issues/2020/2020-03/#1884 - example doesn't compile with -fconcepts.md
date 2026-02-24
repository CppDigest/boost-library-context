# #1884 - example doesn't compile with -fconcepts [Closed]

> Username: gr4yj3d1  
> Created at: 2020-03-25 16:15:37 UTC  
> Updated at: 2020-05-29 08:49:47 UTC  
> Closed at: 2020-04-07 14:17:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1884  

Hi, I'm trying to integrate beast into my project witch uses concepts. But when I tried to compile it, it burried me in errors. So I just tried to compile one of the examples which was closest to my usecase but that doesn't compile either. Is that a bug or is it intended that way? In the latter case: what would I have to change in the example to make it work?  
  
### Version of Beast  
  
BOOST_BEAST_VERSION 277  
  
### Steps necessary to reproduce the problem  
  
- clone complete boost repository into lib/boost  
- checkout tag: boost-1.72.0  
- g++ -std=c++2a -fconcepts -Ilib/boost -Ilib/boost/libs/beast/ lib/boost/libs/beast/example/advanced/server-flex/advanced_server_flex.cpp -lpthread -lssl -lcrypto  
  
### All relevant compiler information  
  
gcc version 9.2.1 20200123 (Debian 9.2.1-25)   
error log: https://gist.github.com/jed1/555abce54f87fd20acfaf2e56749cf1d

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-30 13:21:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1884#issuecomment-605995047  

Hi @jed1, thanks for reporting this and sorry for the delayed response.  
  
I can confirm that I have replicated the bug and am now investigating:  
  
a) whether it's a coding error or a compiler bug  
b) whether there is a fix or workaround.  
  
I'll keep you posted.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-03-30 14:03:00 UTC  
> Updated at: 2020-03-30 14:21:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1884#issuecomment-606018728  

OK, it turns out to be a little more interesting that just a compiler/code bug.  
  
I've tracked down the problem, and it's actually in the Boost.Asio library on which we depend.  
  
# Background  
  
In Concepts.TS, the syntax for writing a concept was : `concept bool ConceptName = Criteria;`  
  
However, in c++20 the accepted syntax is: `concept ConceptName = Critera;`. i.e. the `bool` is implied and you must not spell it out.  
  
Clang accepts both forms of the syntax (but should utter a warning if it sees `concept bool`), but GCC's implementation came much later and is based on the accepted c++20 spec.  
  
There is also a further problem that the method for constraining concepts has evolved since Concepts.TS (but I am by no means an expert on this).  
  
# Reference  
  
https://stackoverflow.com/questions/60684179/visual-studio-2019-rejects-bool-concept-while-gcc-8-doesnt-compile-concepts-w/60684473#60684473  
  
# Workarounds  
  
The recommended workaround (answer offered by Barry Revzin in the page above) is:  
```  
#if __cpp_concepts >= 201707  
  // working paper, C++20 concepts  
  #define CONCEPT concept  
#else  
  // TS  
  #define CONCEPT concept bool  
#endif  
  
template <typename T>  
CONCEPT C = true;  
```  
  
Unfortunately this doesn't help us because of the following lines in `boost/asio/detail/config.hpp:348`:  
  
```  
// Support concepts on compilers known to allow them.  
#if !defined(BOOST_ASIO_HAS_CONCEPTS)  
# if !defined(BOOST_ASIO_DISABLE_CONCEPTS)  
#  if __cpp_concepts  
#   define BOOST_ASIO_HAS_CONCEPTS 1  
#   define BOOST_ASIO_CONCEPT concept bool      // <<==== HERE  
#  endif // __cpp_concepts  
# endif // !defined(BOOST_ASIO_DISABLE_CONCEPTS)  
#endif // !defined(BOOST_ASIO_HAS_CONCEPTS)  
```  
  
## Workaround 1  
  
At the present time, the only non-invasive solution is to disable concepts in compilation units that involve Asio (which will presumably be the majority of them in your program) when you compile with gcc.  
  
## Workaround 2  
  
Modify the ASIO source code as detailed in boostorg/asio#312 - [see this comment](https://github.com/boostorg/asio/issues/312#issuecomment-577303929)  
  
Sorry I can't be of more help.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-04-07 14:17:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1884#issuecomment-610413719  

Great news. Chris has indicated that a fix has been committed to master branch of asio and will be included in the next Boost release.  
  
https://github.com/chriskohlhoff/asio/commit/00157db09efc9ce65e01ea37346a06b3d82d4f65

---

## Comment 4

> Username: gr4yj3d1  
> Created at: 2020-05-29 08:49:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1884#issuecomment-635855251  

I can confirm that Boost-1.73 solved this issue, no workaround needed
