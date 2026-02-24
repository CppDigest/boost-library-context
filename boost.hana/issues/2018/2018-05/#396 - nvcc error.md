# #396 - nvcc error [Closed]

> Username: Char-Aznable  
> Created at: 2018-05-05 04:26:13 UTC  
> Updated at: 2018-09-23 16:09:21 UTC  
> Closed at: 2018-09-23 16:09:21 UTC  
> Url: https://github.com/boostorg/hana/issues/396  

I'm trying to use hana in a CUDA project. I got this compilation error in nvcc (cuda 9.1 + gcc 6.3.0) when compiling the example in hana::map (http://boostorg.github.io/hana/structboost_1_1hana_1_1map.html, right before "Synopsis of associated functions"):  
  
```  
/home/aznb/.linuxbrew/include/boost/hana/fwd/optional.hpp(343): error: defaulted default constructor cannot be constexpr because the corresponding implicitly declared default constructor would not be constexpr  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(37): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(37): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(37): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(38): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(38): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(38): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(39): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(39): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(39): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(42): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(42): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(42): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(45): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(45): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(45): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(48): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(48): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(48): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(51): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(51): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(51): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(54): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(54): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(54): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(55): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(55): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(55): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(56): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(56): here  
  
/home/aznb/.linuxbrew/include/boost/hana/string.hpp(90): error: expression must have a constant value  
          detected during:  
            instantiation of "boost::hana::string_detail::prepare_impl" based on template arguments <tmp, 0UL, 1UL, 2UL, 3UL, 4UL, 5UL, 6UL>   
(97): here  
            instantiation of "decltype(auto) boost::hana::string_detail::prepare(S) [with S=tmp]"   
testmap.cpp(56): here  
  
31 errors detected in the compilation of "/home/aznb/tmp/tmpxft_0002f4bb_00000000-8_testmap.cpp1.ii".  
```  
This looks like a nvcc bug but can someone confirm this? What should I report to Nvidia in order to get their attention to this? Too bad I can't use this library for my project...  
  
Here's the source code and the command line in order to reproduce the error:  
```  
#include <boost/hana/assert.hpp>  
#include <boost/hana/at_key.hpp>  
#include <boost/hana/contains.hpp>  
#include <boost/hana/map.hpp>  
#include <boost/hana/pair.hpp>  
#include <boost/hana/string.hpp>  
#include <functional>  
#include <string>  
#include <vector>  
namespace hana = boost::hana;  
template <typename ...Events>  
struct event_system {  
    using Callback = std::function<void()>;  
    hana::map<hana::pair<Events, std::vector<Callback>>...> map_;  
    template <typename Event, typename F>  
    void on(Event e, F handler) {  
        auto is_known_event = hana::contains(map_, e);  
        static_assert(is_known_event,  
            "trying to add a handler to an unknown event");  
        map_[e].push_back(handler);  
    }  
    template <typename Event>  
    void trigger(Event e) const {  
        auto is_known_event = hana::contains(map_, e);  
        static_assert(is_known_event,  
            "trying to trigger an unknown event");  
        for (auto& handler : this->map_[e])  
            handler();  
    }  
};  
template <typename ...Events>  
event_system<Events...> make_event_system(Events ...events) {  
    return {};  
}  
int main() {  
    auto events = make_event_system(  
        BOOST_HANA_STRING("foo"),  
        BOOST_HANA_STRING("bar"),  
        BOOST_HANA_STRING("baz")  
    );  
    std::vector<std::string> triggered_events;  
    events.on(BOOST_HANA_STRING("foo"), [&] {  
        triggered_events.push_back("foo:1");  
    });  
    events.on(BOOST_HANA_STRING("foo"), [&] {  
        triggered_events.push_back("foo:2");  
    });  
    events.on(BOOST_HANA_STRING("bar"), [&] {  
        triggered_events.push_back("bar:1");  
    });  
    events.on(BOOST_HANA_STRING("baz"), [&] {  
        triggered_events.push_back("baz:1");  
    });  
    events.trigger(BOOST_HANA_STRING("foo"));  
    events.trigger(BOOST_HANA_STRING("bar"));  
    events.trigger(BOOST_HANA_STRING("baz"));  
    BOOST_HANA_RUNTIME_CHECK(triggered_events == std::vector<std::string>{  
        "foo:1", "foo:2", "bar:1", "baz:1"  
    });  
}  
```  
```  
nvcc -x cu testmap.cpp -o testmap -std=c++14  
```  
Note that the error doesn't happen if `-x cu` option is removed.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-05-05 04:52:53 UTC  
> Updated at: 2018-05-05 05:02:42 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386779246  

Your code does not compile on clang either. Just skimming through your error output the problem appears to be with how the `BOOST_HANA_STRING` macro is using a constexpr array or something.  
  
The error that I am getting is caused by your declaring a map type:  
```cpp  
hana::map<hana::pair<Events, std::vector<Callback>>...> map_;  
```  
Not only does this result in too many template arguments, but Boost.Hana makes no guarantee about the template parameters in the `hana::map` template. You have to use `hana::make_map`.  
  
While the documentation does state this:  
"`Note that the actual representation of a hana::map is an implementation detail.`"  
  
It does not appear to be clear in the type signature located above that which should probably be changed to something like:  
**template<typename ...>  
struct boost::hana::map;**  
  
**EDIT**: I just realized you stated that the code is actually an example from the documentation. That is very odd because I was sure that all of the examples get compiled along with all of the tests in CI.

---

## Comment 2

> Username: Char-Aznable  
> Created at: 2018-05-05 05:22:42 UTC  
> Updated at: 2018-05-05 05:26:22 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386780559  

Yes, ```nvcc testmap.cpp -o testmap -std=c++14``` would work but ```nvcc -x cu testmap.cpp -o testmap -std=c++14``` doesn't work because the latter is compiling through CUDA tool chain. I'm pretty sure it's a nvcc bug but it would take someone who's familiar with hana to pin it down.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2018-05-05 05:43:29 UTC  
> Updated at: 2018-05-05 05:50:55 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386781518  

I'm saying that the example is incorrect and does not even compile on Clang.  
  
I'm not sure why it would work on yours at all.  
  
I'm not sure if I will have time to play with this, but it is kind of related to a presentation I am giving at C++Now next week on compiling C++ programs in Docker.  
  
https://hub.docker.com/r/nvidia/cuda/ <-- Is that the toolchain you are using?

---

## Comment 4

> Username: Char-Aznable  
> Created at: 2018-05-05 05:59:10 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386782251  

This example does compile with gcc 6.3.0 on my machine. The cuda 9.1 is the tool chain I'm using. The exact version is:  
```  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2017 NVIDIA Corporation  
Built on Fri_Nov__3_21:07:56_CDT_2017  
Cuda compilation tools, release 9.1, V9.1.85  
```

---

## Comment 5

> Username: Char-Aznable  
> Created at: 2018-05-05 06:09:00 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386782693  

Also, doesn't the first line in the error message look like a compiler bug assuming that hana's definition of constexpr function meets the c++14 requirement?  
```  
/home/aznb/.linuxbrew/include/boost/hana/fwd/optional.hpp(343): error: defaulted default constructor cannot be constexpr because the corresponding implicitly declared default constructor would not be constexpr  
```  
What is the error you've seen on Clang?

---

## Comment 6

> Username: ricejasonf  
> Created at: 2018-05-05 06:19:44 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386783140  

```  
clang version 3.9.0 (http://llvm.org/git/clang.git 116ce8458879ac0721cfd7badfd4c3868ed2da78) (http://llvm.org/git/llvm.git 0a04f70a4b425044237117dc5bbe462124ad82fb)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/local/bin  
```  
  
```  
jason@jason-nuc:~/temp/experiments$ clang++ -stdlib=libc++ -std=c++14 aznable.cpp   
aznable.cpp:15:11: error: too many template arguments for class template 'map'  
    hana::map<hana::pair<Events, std::vector<Callback>>...> map_;  
          ^   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
aznable.cpp:37:19: note: in instantiation of template class 'event_system<boost::hana::string<'f', 'o',  
      'o'>, boost::hana::string<'b', 'a', 'r'>, boost::hana::string<'b', 'a', 'z'> >' requested here  
    auto events = make_event_system(  
                  ^  
/usr/local/include/boost/hana/map.hpp:75:12: note: template is declared here  
    struct map  
           ^  
1 error generated.  
```

---

## Comment 7

> Username: ricejasonf  
> Created at: 2018-05-05 06:24:55 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386783363  

I am mistaken. I must be on a really old version of Boost.Hana.  
  
```  
commit e3e8736aabd89c614bb26757eb31627e5c03fdb3  
Author: Louis Dionne <ldionne.2@gmail.com>  
Date:   Mon Nov 14 17:52:44 2016 -0800  
  
    [map] Allow declaring maps using map<Pairs...>, and provide a variadic constructor  
```

---

## Comment 8

> Username: ldionne  
> Created at: 2018-05-05 14:40:12 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386810213  

I don't have access to NVCC and it's not currently a supported compiler. I can't make any guarantees about it, unfortunately. Your example appears to be correct, though, so it's either a bug in Hana or a bug in the compiler.  
  
Have you tried building Hana's tests and examples? How many failures do you get? If you get a lot of unrelated failures, it may just be that NVCC is not sufficiently standards conforming to support Hana.

---

## Comment 9

> Username: Char-Aznable  
> Created at: 2018-05-05 17:23:54 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-386821510  

I tried a couple of examples in the tutorial and most of them failed on the error in the first line listed above and I gave up.

---

## Comment 10

> Username: ldionne  
> Created at: 2018-09-23 16:09:21 UTC  
> Url: https://github.com/boostorg/hana/issues/396#issuecomment-423827368  

Closing since NVCC is not a supported compiler.
