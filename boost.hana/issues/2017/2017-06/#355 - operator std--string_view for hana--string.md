# #355 - operator std::string_view for hana::string [Closed]

> Username: bebuch  
> Created at: 2017-06-16 12:22:04 UTC  
> Updated at: 2020-04-21 19:24:07 UTC  
> Closed at: 2017-08-27 19:31:25 UTC  
> Url: https://github.com/boostorg/hana/issues/355  

When I use libstdc++ I can not trivially create a std::string_view in constexpr context by a c_str. (https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78156)  
  
Therefor and for comfort reason it would be nice to have a conversion operator like in std::string.  
  
I implemented a C++17 version with commit afde8d819950eb245503490f28816e061a038984. Unfortunately I have no idea how to detect if string_view exists in C++14 mode :-/  
  
Do you think this is an good idea and how to implement it backward compatible to C++14?

---

## Comment 1

> Username: ldionne  
> Created at: 2017-06-16 16:03:00 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-309065798  

The reason why `hana::string` does not provide implicit conversions to either `std::string` or `std::string_view` is that it's a lossy conversion. Indeed, `hana::string` contains the fact that the value is known at compile-time while both `std::string` and `std::string_view` do not.

---

## Comment 2

> Username: bebuch  
> Created at: 2017-06-16 17:25:54 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-309085517  

But it provides a c_str() function which is also lossy. ;-) (yes, it's not completely comparable)  
  
`std::string` and `std::string_view` both provide a constructor which takes a c_str, but both need to count the characters at runtime if I don't provide the size as second argument. What do you think about free (and explicit) conversion functions?  
  
```  
template < typename String >  
constexpr std::string_view to_std_string_view(String const& s)noexcept{  
    static_assert(hana::is_a< hana::string_tag, String >());  
    return {s.c_str(), hana::size(s)};  
}  
```  
  
If you don't want to directly add something like this to the source, I think it should be (as a hint) part of the documentation. Without this most people will just use c_str() to create a string or string_view, without noticing that they loss runtime speed.  
  
I would prefer an implementation as part of hana, because with knowing the implementation, the `static_assert` (or an equivalent SFINAE with enable_if) is unnecessary. Also the user side overloading for other types would be simpler.

---

## Comment 3

> Username: ldionne  
> Created at: 2017-06-18 19:33:19 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-309297839  

> But it provides a c_str() function which is also lossy. ;-) (yes, it's not completely comparable)  
  
The whole point is that this `c_str()` function provides an explicit conversion, not an implicit one.  
  
  
  
> Without this most people will just use c_str() to create a string or string_view, without noticing that they loss runtime speed.  
  
I'd be _very_ intrigued to see whether there's actually any cost to this. I would expect the compiler to just see straight through it since the returned `char const*` is constant and produce optimal code.  
  
If there is indeed a potential performance pitfall, what I could do is provide `hana::to<std::string_view>` and `hana::to<std::string>` for convenience. Can you please provide data on whether conversion through a `std::string_view` from a `hana::string::c_str` is optimal?  
  
Also, the reason for not just saying "yes" and adding those functions is to avoid bloating the interface.

---

## Comment 4

> Username: bebuch  
> Created at: 2017-06-19 13:03:47 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-309433035  

Good point, I will analyze it and post the results here.

---

## Comment 5

> Username: bebuch  
> Created at: 2017-06-19 15:40:52 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-309479441  

I've some interesting results. As you expected, clang optimizes the overhead away. GCC however does not.  
  
First I compiled to asm and and saw that clang produced the same output for the corresponding cases, while GCC's results are a little different.  
  
Since I'm not good in reading assembler I wrote a benchmark with Google Benchmark.  
  
https://github.com/google/benchmark  
  
```  
#include <boost/hana.hpp>  
  
#include <benchmark/benchmark.h>  
  
#include <string_view>  
#include <string>  
  
  
namespace hana = boost::hana;  
using namespace hana::literals;  
  
auto const text = "some compile time text"_s;  
  
  
/// \brief Converts a hana::string to std::string_view  
template < typename String,  
	typename = std::enable_if< hana::is_a< hana::string_tag, String >() > >  
constexpr std::string_view to_std_string_view(String const& s)noexcept{  
	return {s.c_str(), hana::size(s)};  
}  
  
/// \brief Converts a hana::string to std::string  
template < typename String,  
	typename = std::enable_if< hana::is_a< hana::string_tag, String >() > >  
std::string to_std_string(String const& s){  
	return {s.c_str(), hana::size(s)};  
}  
  
  
[[gnu::noinline]]  
void nothing(benchmark::State& state){  
	while(state.KeepRunning()){  
		auto test{text};  
		benchmark::DoNotOptimize(test);  
	}  
}  
  
[[gnu::noinline]]  
void string_c_str(benchmark::State& state){  
	while(state.KeepRunning()){  
		auto test{std::string(text.c_str())};  
		benchmark::DoNotOptimize(test);  
	}  
}  
  
[[gnu::noinline]]  
void string_convert(benchmark::State& state){  
	while(state.KeepRunning()){  
		auto test{to_std_string(text)};  
		benchmark::DoNotOptimize(test);  
	}  
}  
  
[[gnu::noinline]]  
void string_view_c_str(benchmark::State& state){  
	while(state.KeepRunning()){  
		auto test{std::string_view(text.c_str())};  
		benchmark::DoNotOptimize(test);  
	}  
}  
  
[[gnu::noinline]]  
void string_view_convert(benchmark::State& state){  
	while(state.KeepRunning()){  
		auto test{to_std_string_view(text)};  
		benchmark::DoNotOptimize(test);  
	}  
}  
  
  
BENCHMARK(nothing);  
BENCHMARK(string_c_str);  
BENCHMARK(string_convert);  
BENCHMARK(string_view_c_str);  
BENCHMARK(string_view_convert);  
  
BENCHMARK_MAIN();  
  
```  
  
```  
$clang++-5 -std=c++1z -Wno-gnu-string-literal-operator-template \  
-I ~/media/libraries/src/boost -I ~/media/libraries/share/clang/include/ \  
-DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -O3 -o main hana.cpp \  
-stdlib=libc++ -L ~/media/libraries/share/clang/lib/ -lbenchmark -lpthread  
Run on (8 X 4200 MHz CPU s)  
2017-06-19 17:27:58  
***WARNING*** CPU scaling is enabled, the benchmark real time measurements may  
be noisy and will incur extra overhead.  
-----------------------------------------------------------  
Benchmark                    Time           CPU Iterations  
-----------------------------------------------------------  
nothing                      1 ns          1 ns  634200787  
string_c_str                 1 ns          1 ns  580064429  
string_convert               1 ns          1 ns  560823644  
string_view_c_str            1 ns          1 ns  711289899  
string_view_convert          1 ns          1 ns  683581006  
  
```  
  
```  
$g++-7.1 -std=c++1z -Wno-gnu-string-literal-operator-template \  
-I ~/media/libraries/src/boost -I ~/media/libraries/share/include/ \  
-DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -O3 -o main hana.cpp \  
-L ~/media/libraries/share/lib/ -lbenchmark -lpthread  
Run on (8 X 4200 MHz CPU s)  
2017-06-19 17:28:32  
***WARNING*** CPU scaling is enabled, the benchmark real time measurements may  
be noisy and will incur extra overhead.  
-----------------------------------------------------------  
Benchmark                    Time           CPU Iterations  
-----------------------------------------------------------  
nothing                      1 ns          1 ns  611292732  
string_c_str                22 ns         22 ns   31442604  
string_convert              17 ns         17 ns   42990708  
string_view_c_str            4 ns          4 ns  186673902  
string_view_convert          1 ns          1 ns  610187805  
```  
  
If I interpreted the assembler right, clang optimized also the memory allocation of `std::string` away.

---

## Comment 6

> Username: bebuch  
> Created at: 2017-08-02 15:40:10 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-319711170  

Hi Louis, did my benchmark convince you to provide `hana::to<std::string_view>` and `hana::to<std::string>` or do you have any remarks to it?

---

## Comment 7

> Username: ricejasonf  
> Created at: 2017-08-03 03:54:38 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-319860968  

I was playing with clang and string_view (`sv`) literals vs making a string_view with a string literal and not specifying the size, and it was generating more assembly with the latter so your benchmark for clang is interesting, but maybe they added optimizations recently (idk).  
  
Forgive me for putting my two cents in, but you did already demonstrate that this could be done in a single line of code:  
  
```cpp  
constexpr std::string_view foo{str.c_str(), hana::size(str)};  
```

---

## Comment 8

> Username: bebuch  
> Created at: 2017-08-03 09:50:56 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-319923231  

My problem is, that it is simple to forget the second argument because it will also compile without it. Only the performance might be affected.  
  
On the other hand I understand the problem with code bloat. A simple hint in the documentation of hana::string how to do it best would be also fine for me.

---

## Comment 9

> Username: ldionne  
> Created at: 2017-08-27 19:31:25 UTC  
> Updated at: 2017-08-27 20:03:50 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-325219155  

Sorry for the extended delay. I wanted to have a proper look at this and I only got around to doing it yesterday. Here's what I found out: https://godbolt.org/g/oPAsbN  
  
The left assembly window has GCC, and you can switch panes to check the assembly generated for with and without the size hint being given to the `string_view` constructor. The right assembly window is the same, but for Clang.  
  
Basically, Clang produces exactly the same assembly for both  
  
```c++  
auto const text = "foo"_s;  
std::string_view s{text.c_str(), hana::size(text)};  
```  
  
and  
  
```c++  
auto const text = "foo"_s;  
std::string_view s{text.c_str()};  
```  
  
But GCC does not. Since this is a failed optimization that would be generally useful (so not only useful for Hana), this should be treated as a GCC QOI issue and reported to them instead. For the following reasons, I'm not going to change Hana:  
- Bloats the interface for Hana.  
- This is a QOI issue in a compiler, and the result of fixing this in the compiler is going to be generally useful.  
- If we add these conversion functions, we could go ahead and add special-cased conversions for many different standard library types as well. I just don't want to go down that route of being so specific.  
  
I have reported the bug to GCC [here](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=82000).

---

## Comment 10

> Username: jonesmz  
> Created at: 2020-03-15 09:50:20 UTC  
> Updated at: 2020-04-21 19:24:07 UTC  
> Url: https://github.com/boostorg/hana/issues/355#issuecomment-599188660  

I'd like to ask for this issue to be reconsidered.  
  
I'm trying to adopt `boost::hana` into an existing large codebase, and `std::string_view` is used all over the place to accept strings of various types as a function parameter, both in runtime functions, and constexpr functions.  
  
I already have an existing meta string similar to (but not as robust as) `hana::string`, but mine implicitly converts to `std::string_view`. As a result, switching to `hana::string` requires many thousands of lines of code to be modified to add explicit conversions. That's simply not going to happen.  
  
In addition to the quantity of code that would need to be modified, this is also problematic because it would interfere with later changes to the functions that currently accept `std::string_view` to instead accept `hana::string`, or template parameters, or what have you.  
  
C++ doesn't provide a mechanism for the programmer to provide a User Defined Conversion (https://en.cppreference.com/w/cpp/language/cast_operator) between types that the programmer doesn't control. I can't add a constructor to `std::string_view` to make it accept a `hana::string`, and I can't add an implicit conversion operator to `hana::string` unless I fork `boost::hana`, which I would really rather not do.  
  
While I completely understand your perspective on not bloating `boost::hana`'s interface, and not having to go down the path of adding convenience conversions for every standard library type, there's also the argument that a library does have to ultimately be usable by end users, and `hana::string`'s interface is lacking for general usage.  
  
Also, it's possible to convert from a constexpr `std::string_view` back into a `hana::string`. As a result, an implicit conversion to `std::string_view` does not lose the fact that the value is known at compile time, so long as the resulting `std::string_view` is used only in constexpr contexts.  
  
https://godbolt.org/z/WEPXBP  
  
```cpp  
#include <boost/hana/string.hpp>  
#include <string>  
#include <iostream>  
#include <string_view>  
  
template<const char* str>  
static inline void print(void)  
{  
    std::cout << str << std::endl;  
}  
  
template<typename METASTR,  size_t ... INDICIES>  
constexpr auto build_hana_string_from_string_literal(std::index_sequence<INDICIES...>)  
{  
    return boost::hana::string<METASTR{}.chars[INDICIES]...>{};  
}  
  
template<typename METASTR>  
constexpr auto build_hana_string_from_string_literal(void)  
{  
    constexpr size_t length = std::char_traits<char>::length(METASTR{}.chars);  
    return build_hana_string_from_string_literal<METASTR>(std::make_index_sequence<length>{});  
}  
  
int main()  
{  
    static constexpr auto str = BOOST_HANA_STRING("SomeStringHere");  
    static constexpr std::string_view sview{str.c_str()};  
    struct HanaMetaStr { const char * const chars = sview.substr(4).data(); };  
    static constexpr auto backToHanaString = build_hana_string_from_string_literal<HanaMetaStr>(std::make_index_sequence<6>{});  
    static constexpr auto someOtherHanaString = BOOST_HANA_STRING("SomeOtherString");  
    print<(backToHanaString + someOtherHanaString).c_str()>();  
  
    return 0;  
}  
```  
  
Tested with Clang 9  
  
Everything except for the call to the `print<>()` function works in GCC 9.2 as well, though I'm not sure why GCC doesn't like the print<>() function.
