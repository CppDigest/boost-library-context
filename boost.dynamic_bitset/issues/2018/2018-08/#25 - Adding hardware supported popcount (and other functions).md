# #25 - Adding hardware supported popcount (and other functions) [Closed]

> Username: Izaron  
> Created at: 2018-08-23 13:51:04 UTC  
> Updated at: 2018-10-13 21:48:11 UTC  
> Closed at: 2018-10-13 21:48:10 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/25  

[Mailing list discussion thread](https://lists.boost.org/Archives/boost/2018/08/242825.php), there are many related things.  
  
I was working with the Boost.DynamicBitset library and it turned out that the library doesn't use hardware supported fast bit operations when it could be possible, apparently due to dates when some functions were written (about ~15 years ago).  
  
I found 2 bottlenecks so far:  
  
1. Counting the number of bits in an integer. It's currently done via a handcrafted table where are all the 256 precalculated values are written. [Link to the code].  
2. Another serious thing - finding the position of the lowest bit 'on' [here] uses calculations "by hand", though it would work well with inlined `__builtin_ctz(x)` (on GCC).  
  
I've done a pull request to solve the first issue using compile-time stuff. If the block type is smaller than needed for popcount functions, then it uses the old way (otherwise it obviously works slower).  
  
I tested builtin popcount function with `example/timing_tests.cpp` and got these results: [**before**](https://gist.githubusercontent.com/Izaron/24489ad8f3b33f2a79d126b605072cae/raw/b0ec122773674a1ac0d585244c8d6149919596df/Before),  [**after**](https://gist.githubusercontent.com/Izaron/a6f0716aa1fb5c1eb065e1ae706d9340/raw/e30bfc13744797fc139708ec327181abab418fae/After). As you can see, long types run faster. Testing only `.count(x)` function (without creating many object) **shows x2 times speed**.  
My computer runs on Debian 9, its CPU is AMD E1-2500 (old and weak mid-2013 stuff).  
  
**TODO**:  
  
- I don't have Windows to test its code (the Windows code in the PR I wrote looking at other libraries' code and using common sense). Apparently it has to have runtime checks calling `__cpuid` - [docs](https://docs.microsoft.com/en-us/cpp/intrinsics/popcnt16-popcnt-popcnt64): _To determine hardware support <...> If you run code that uses this intrinsic on hardware that does not support the popcnt instruction, the results are unpredictable_. I hope online build systems have different Windows systems to test, otherwise I'll need help from people who have Windows.  
  
- Inline the `lowest_bit` function if possible. (The second bottleneck, after the first become verified).  
  
- Proofreading the compile-time stuff from people who have rich experience in such things and can find a mistake like "On a Penryn, \_\_AVX\_\_ will give a false negative."  
  
- Avoid copy-pasting preprocessor directives - it's better to keep a short alias somewhere at the top of a header file.  
  
Discussion is welcome.  
  
[Link to the code]: https://github.com/boostorg/dynamic_bitset/blob/boost-1.64.0/include/boost/detail/dynamic_bitset.hpp#L106-L148  
[here]: https://github.com/boostorg/dynamic_bitset/blob/boost-1.64.0/include/boost/pending/lowest_bit.hpp#L24-L33

---

## Comment 1

> Username: jeking3  
> Created at: 2018-10-12 12:03:20 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/25#issuecomment-429302849  

I have merged #25 into master for the next release.  You can see the test matrix on develop here, which has a number of different Windows builds (msvc 8.0 through 14.1):  
  
https://www.boost.org/development/tests/develop/developer/dynamic_bitset.html

---

## Comment 2

> Username: Izaron  
> Created at: 2018-10-13 21:48:10 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/25#issuecomment-429577972  

Cool, thanks!
