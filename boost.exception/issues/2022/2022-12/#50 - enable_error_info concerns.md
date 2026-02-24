# #50 - enable_error_info concerns [Closed]

> Username: zlojvavan  
> Created at: 2022-12-19 08:06:41 UTC  
> Updated at: 2022-12-23 08:39:13 UTC  
> Closed at: 2022-12-23 07:00:50 UTC  
> Url: https://github.com/boostorg/exception/issues/50  

hello  
I wonder what's the point of copying exception object "If T derives from boost::[exception]" instead of returning it (to throw later) as is?

---

## Comment 1

> Username: zajo  
> Created at: 2022-12-19 23:00:12 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1358526341  

This is a very old library from before we had move. What is the concern with the copy? Presumably we're using this just before we throw, so any concerns about speed are probably not warranted. Also, if you pass an unnamed temporary to `enable_error_info`, the copy constructor is elided.

---

## Comment 2

> Username: zlojvavan  
> Created at: 2022-12-20 07:07:38 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1358923765  

just the fact that there's unnecessary copy operation that probably could be avoided even without move semantics if that's what you meant  
  
well, yes I used it before uniformly but figured it does unnecessary copy so I've had to separate my throw helper in two distinct ones  
  
`		template <typename T>  
		[[noreturn]]  
		std::enable_if_t< std::is_base_of_v<boost::exception, T>> ThrowExceptionImpl(T const& be, char const* current_function, char const* file, int line)  
		{  
			ProcessErrorInfoContainer(be);  
...  
                }  
		template <typename T>  
		[[noreturn]]  
		std::enable_if_t< !std::is_base_of_v<boost::exception, T> > ThrowExceptionImpl(T const& e, char const* current_function, char const* file, int line)  
		{  
			ThrowExceptionImpl(boost::enable_error_info(e), current_function, file, line);  
		}  
`  
  
thought you would be interested to optimize it away without relying upon copy elision

---

## Comment 3

> Username: zajo  
> Created at: 2022-12-20 21:02:43 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1360238355  

What is the concern, the the size of the generated code or the speed?

---

## Comment 4

> Username: zlojvavan  
> Created at: 2022-12-21 07:10:39 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1360936666  

1. the fact there's unnecessary copy operation is enough for me  
2. I'm not so sure about "if you pass an unnamed temporary to enable_error_info, the copy constructor is elided",  see [https://godbolt.org/z/vvdj9ooY3](https://godbolt.org/z/vvdj9ooY3)  
  
according to [Explanation](https://en.cppreference.com/w/cpp/language/throw)  
 "1) First, [copy-initializes](https://en.cppreference.com/w/cpp/language/copy_initialization) the exception object from expression" and though there's also "The copy/move (since C++11) may be subject to [copy elision](https://en.cppreference.com/w/cpp/language/copy_elision)" part and my example might be wrong IIRC in my experience I observed copy operation  
  
p.s. despite being "old" I find that lib very useful and wish all boost libs used boost::exception/throw_exception and boost/std offered customization points for throwing exceptions (as already asked in https://github.com/boostorg/exception/issues/25#issuecomment-587312449) as some environments such as codegear/embarcadero allow

---

## Comment 5

> Username: zajo  
> Created at: 2022-12-21 20:26:32 UTC  
> Updated at: 2022-12-21 20:29:42 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1362056725  

I also checked whether the copy elision happens, but it doesn't because it turns out `std::~exception` is defined out of line. Actually if you use `boost::throw_exception` there is no problem right? It's got its own mechanism for injecting `boost::exception` as a base.

---

## Comment 6

> Username: zlojvavan  
> Created at: 2022-12-22 06:36:16 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1362471897  

so apparently I was right that copy elision on throw is wishful thinking  
and boost::throw_exception(derived_exception()) still invokes copy constructor and increments the counter

---

## Comment 7

> Username: zajo  
> Created at: 2022-12-23 01:23:27 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1363494573  

Anyway, I don't see a practical problem with any of this.  
  
Have you considered using Boost LEAF instead? It is a better solution to the same problem, assuming its limitations are not an issue for your use case.

---

## Comment 8

> Username: zlojvavan  
> Created at: 2022-12-23 06:46:39 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1363668321  

well of course it's up to you whether to fix it or not, though probably cost of copying exception in some derived hierarchies might be not so low. at least I think I did my best to highlight the problem  
  
I believe I considered LEAF couple of years ago and while I find it really interesting and will probably start using it in new projects some day I guess it cannot replace current infrastructure (including boost/exception) in existing projects/libs

---

## Comment 9

> Username: zajo  
> Created at: 2022-12-23 07:00:50 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1363675071  

I prefer to not make unnecessary changes to Boost Exception, as I can not test with old MSVC versions.  
  
[Here](https://www.boost.org/doc/libs/release/libs/leaf/doc/html/index.html#boost_exception) is guide on how to migrate from Boost Exception to LEAF, it's straight forward.  
  
Also, you can [handle Boost Exception error info](https://www.boost.org/doc/libs/release/libs/leaf/doc/html/index.html#tutorial-boost_exception_integration) with LEAF.

---

## Comment 10

> Username: zlojvavan  
> Created at: 2022-12-23 08:39:12 UTC  
> Url: https://github.com/boostorg/exception/issues/50#issuecomment-1363738785  

even if it's "straight forward" atm there's no motivation and sometimes technical feasibility to refactor all existing sources to migrate to LEAF
