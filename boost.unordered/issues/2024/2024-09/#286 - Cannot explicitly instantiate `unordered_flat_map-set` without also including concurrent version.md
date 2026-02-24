# #286 - Cannot explicitly instantiate `unordered_flat_map/set` without also including concurrent version [Closed]

> Username: chriselrod  
> Created at: 2024-09-14 21:20:13 UTC  
> Updated at: 2024-10-23 16:47:31 UTC  
> Closed at: 2024-10-23 14:47:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/286  

Minimal reproducer:  
```sh  
$ cat boost_unordered.cpp   
```  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
  
template class boost::unordered_flat_map<int,int>;  
  
int main(){ return 0; }  
```  
```sh  
$ g++ boost_unordered.cpp  
In file included from boost_unordered.cpp:1:  
/usr/include/boost/unordered/unordered_flat_map.hpp: In instantiation of ‘boost::unordered::unordered_flat_map<Key, T, Hash, KeyEqual, Allocator>::unordered_flat_map(boost::unordered::concurrent_flat_map<Key, T, Hash, KeyEqual, Allocator>&&) [with Key = int; T = int; Hash = boost::hash<int>; KeyEqual = std::equal_to<int>; Allocator = std::allocator<std::pair<const int, int> >]’:  
boost_unordered.cpp:3:23:   required from here  
    3 | template class boost::unordered_flat_map<int,int>;  
      |                       ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/unordered/unordered_flat_map.hpp:186:36: error: invalid use of incomplete type ‘class boost::unordered::concurrent_flat_map<int, int, boost::hash<int>, std::equal_to<int>, std::allocator<std::pair<const int, int> > >’  
  186 |           : table_(std::move(other.table_))  
      |                              ~~~~~~^~~~~~  
In file included from /usr/include/boost/unordered/unordered_flat_map.hpp:13:  
/usr/include/boost/unordered/concurrent_flat_map_fwd.hpp:31:11: note: declaration of ‘class boost::unordered::concurrent_flat_map<int, int, boost::hash<int>, std::equal_to<int>, std::allocator<std::pair<const int, int> > >’  
   31 |     class concurrent_flat_map;  
      |           ^~~~~~~~~~~~~~~~~~~  
```  
  
This is with boost 1.86. I get the error with both gcc 14 and clang 18.  
  
The problem is that explicit instantiation tries to instantiate this method, referencing a member of an incomplete type:  
https://github.com/boostorg/unordered/blob/57546ed7e38d40fdef7ae27d15522c706260d5d0/include/boost/unordered/unordered_flat_map.hpp#L185-L189  
  
Unfortunately, explicit instantiation is necessary when using c++20 modules with Clang to work around #261.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-09-15 00:31:03 UTC  
> Url: https://github.com/boostorg/unordered/issues/286#issuecomment-2351248120  

To fix this, you'd have to include the concurrent_flat_map header, too:  
```  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <boost/unordered/concurrent_flat_map.hpp>  
  
template class boost::unordered_flat_map<int,int>;  
  
int main(){ return 0; }  
```

---

## Comment 2

> Username: chriselrod  
> Created at: 2024-09-15 14:53:27 UTC  
> Updated at: 2024-09-15 14:54:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/286#issuecomment-2351626024  

> To fix this, you'd have to include the concurrent_flat_map header, too:  
  
This adds a TBB dependency to my GCC (but not for Clang). I suspect there is a `#include <execution>` somewhere.

---

## Comment 3

> Username: joaquintides  
> Created at: 2024-09-15 15:39:42 UTC  
> Url: https://github.com/boostorg/unordered/issues/286#issuecomment-2351646581  

Just trying to understand the scenario... this particular problem could be solved by adding  
```cpp  
template<bool avoid_explicit_instantiation = true>  
```  
to the offending constructor. How useful is an explicitly instantiated container in your scenario? Many of its member functions are templatized, so does that mean they won't be seen by user code when the container is imported from a module?

---

## Comment 4

> Username: chriselrod  
> Created at: 2024-09-15 17:55:13 UTC  
> Updated at: 2024-09-15 17:55:54 UTC  
> Url: https://github.com/boostorg/unordered/issues/286#issuecomment-2351701677  

> How useful is an explicitly instantiated container in your scenario?  
  
I'm not currently able to build using C++ modules due to other problems.  
However, while I was working on that, one of the problems I encountered was solved by explicitly instantiating the container.  
  
A function that called a `static` non-member functions (i.e., internal linkage, same probably applied to anonymous namespace, but I didn't check) could not be called when compiled in a module using Clang, unless that function was explicitly instantiated.  
  
This was a problem in particular for many `immintrin.h` functions, which are declared `static` in `libstdc++`'s header.  
  
So, I wasn't explicitly instantiating for the usual reason of saving compile times by trying to compile only once, but because it prevented modules from working at all.  
  
Here is an example stack trace, that I just reproduced on a recent clang master:  
```  
/usr/bin/clang++ -DUSE_MODULE -I/home/chriselrod/Documents/progwork/cxx/LoopModels/extern/Math -I/home/chriselrod/Documents/progwork/cxx/LoopModels/extern/Math/include -g -std=gnu++23 -fno-exceptions -fno-rtti -fstack-protector-strong -ferror-limit=8 -fcolor-diagnostics -ftime-trace -march=native -mprefer-vector-width=512 -MD -MT LoopModels/CMakeFiles/LoopModelsModules.dir/IR/Cache.cxx.o -MF LoopModels/CMakeFiles/LoopModelsModules.dir/IR/Cache.cxx.o.d @LoopModels/CMakeFiles/LoopModelsModules.dir/IR/Cache.cxx.o.modmap -o LoopModels/CMakeFiles/LoopModelsModules.dir/IR/Cache.cxx.o -c /home/chriselrod/Documents/progwork/cxx/LoopModels/mod/IR/Cache.cxx  
In module 'Trie' imported from /home/chriselrod/Documents/progwork/cxx/LoopModels/mod/IR/Cache.cxx:66:  
/usr/include/boost/unordered/detail/foa/core.hpp:293:7: error: no matching function for call to '_mm_cmpeq_epi8'  
  293 |       _mm_cmpeq_epi8(load_metadata(),_mm_setzero_si128()))&0x7FFF;  
      |       ^~~~~~~~~~~~~~  
/usr/include/boost/unordered/detail/foa/core.hpp:2088:21: note: in instantiation of member function 'boost::unordered::detail::foa::group15<boost::unordered::detail::foa::plain_integral>::match_available' requested here  
 2088 |       auto mask=pg->match_available();  
      |                     ^  
/usr/include/boost/unordered/detail/foa/core.hpp:1677:14: note: in instantiation of function template specialization 'boost::unordered::detail::foa::table_core<boost::unordered::detail::foa::flat_map_types<IR::InstByValue, IR::Compute *>, boost::unordered::detail::foa::group15<boost::unordered::detail::foa::plain_integral>, boost::unordered::detail::foa::table_arrays, boost::unordered::detail::foa::plain_size_control, boost::hash<IR::InstByValue>, std::equal_to<IR::InstByValue>, alloc::Mallocator<std::pair<const IR::InstByValue, IR::Compute *>>>::nosize_unchecked_emplace_at<boost::unordered::detail::foa::try_emplace_args_t, IR::InstByValue>' requested here  
 1677 |     auto res=nosize_unchecked_emplace_at(  
      |              ^  
/usr/include/boost/unordered/detail/foa/table.hpp:530:17: note: in instantiation of function template specialization 'boost::unordered::detail::foa::table_core<boost::unordered::detail::foa::flat_map_types<IR::InstByValue, IR::Compute *>, boost::unordered::detail::foa::group15<boost::unordered::detail::foa::plain_integral>, boost::unordered::detail::foa::table_arrays, boost::unordered::detail::foa::plain_size_control, boost::hash<IR::InstByValue>, std::equal_to<IR::InstByValue>, alloc::Mallocator<std::pair<const IR::InstByValue, IR::Compute *>>>::unchecked_emplace_at<boost::unordered::detail::foa::try_emplace_args_t, IR::InstByValue>' requested here  
  530 |           this->unchecked_emplace_at(pos0,hash,std::forward<Args>(args)...)),  
      |                 ^  
/usr/include/boost/unordered/detail/foa/table.hpp:364:12: note: in instantiation of function template specialization 'boost::unordered::detail::foa::table<boost::unordered::detail::foa::flat_map_types<IR::InstByValue, IR::Compute *>, boost::hash<IR::InstByValue>, std::equal_to<IR::InstByValue>, alloc::Mallocator<std::pair<const IR::InstByValue, IR::Compute *>>>::emplace_impl<boost::unordered::detail::foa::try_emplace_args_t, IR::InstByValue>' requested here  
  364 |     return emplace_impl(  
      |            ^  
/usr/include/boost/unordered/unordered_flat_map.hpp:501:23: note: in instantiation of function template specialization 'boost::unordered::detail::foa::table<boost::unordered::detail::foa::flat_map_types<IR::InstByValue, IR::Compute *>, boost::hash<IR::InstByValue>, std::equal_to<IR::InstByValue>, alloc::Mallocator<std::pair<const IR::InstByValue, IR::Compute *>>>::try_emplace<IR::InstByValue>' requested here  
  501 |         return table_.try_emplace(std::move(key)).first->second;  
      |                       ^  
```  
This goes away with an explicit instantiation.  
  
However, that is a Clang bug that will hopefully be fixed in the not too distant future. https://github.com/llvm/llvm-project/issues/98021

---

## Comment 5

> Username: joaquintides  
> Created at: 2024-09-19 17:29:37 UTC  
> Url: https://github.com/boostorg/unordered/issues/286#issuecomment-2361791302  

Thanks for the explanation. My proposal would be to then wait and see if https://github.com/llvm/llvm-project/issues/98021 gets fixed soon, otherwise I guess we can go with the `template<bool avoid_explicit_instantiation = true>`, does that sound ok to you?

---

## Comment 6

> Username: joaquintides  
> Created at: 2024-10-23 16:47:29 UTC  
> Url: https://github.com/boostorg/unordered/issues/286#issuecomment-2432837722  

@chriselrod can you confirm if you can succesfully use modules with Boost.Unordered after this change? Thank you!
