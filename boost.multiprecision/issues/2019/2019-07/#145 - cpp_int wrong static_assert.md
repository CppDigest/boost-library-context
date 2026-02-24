# #145 - cpp_int wrong static_assert [Closed]

> Username: mekhontsev  
> Created at: 2019-07-18 08:52:14 UTC  
> Updated at: 2019-07-19 18:43:51 UTC  
> Closed at: 2019-07-19 18:43:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/145  

Tried to use boost::multiprecision::cpp_int with WebAssembly clang compiler (v9)  
It produces 32 bit code and BOOST_HAS_INT128 is correctly defined.  
**sizeof(boost::multiprecision::limb_type) = 8** due to BOOST_HAS_INT128  
**sizeof (limb_data)=8** because it is a struct with two 32 bit members  
  
**internal_limb_count=sizeof(limb_data) / sizeof(limb_type)=1**  
  
compilation of my project failed: cpp_int.hpp, line 1236  
```cpp  
BOOST_STATIC_ASSERT(base_type::internal_limb_count >= 2);   
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-07-19 09:41:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/145#issuecomment-513165872  

This works for me with emcc (which appears not to support __int128), what compiler and command line are you using?

---

## Comment 2

> Username: mekhontsev  
> Created at: 2019-07-19 10:31:58 UTC  
> Updated at: 2019-07-19 14:26:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/145#issuecomment-513180713  

> This works for me with emcc (which appears not to support __int128), what compiler and command line are you using?  
  
Yes, it works with the current Fastcomp backend (clang 6.0), but it fails with the new LLVM upstream backend (clang 9.0): [https://v8.dev/blog/emscripten-llvm-wasm](https://v8.dev/blog/emscripten-llvm-wasm)  
  
The command line looks like:  
`%EMSDK%\upstream\emscripten\em++ -I../boost wasm_test1.cpp`

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-07-19 11:37:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/145#issuecomment-513197231  

Still not getting it.  I have:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
  
static_assert(sizeof(boost::multiprecision::limb_type) == 8, "Oops");  
  
int main(int argc, char **argv)   
{  
   boost::multiprecision::cpp_int i(23);  
   i << 400;  
   boost::multiprecision::uint128_t a(2);  
   boost::multiprecision::uint256_t b(2);  
   boost::multiprecision::uint512_t c(2);  
}  
```  
and  
```  
jzm@jzm-vm:~/boost/boost$ $EMSDK/upstream/emscripten/em++ --version  
emcc (Emscripten gcc/clang-like replacement) 1.38.39 (commit a51302ba20697f5251a8185cfdc72e72b14351e4)  
```  
  
Which builds just fine.

---

## Comment 4

> Username: mekhontsev  
> Created at: 2019-07-19 11:59:29 UTC  
> Updated at: 2019-07-19 11:59:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/145#issuecomment-513202645  

I can confirm that your example builds fine, but it fails if your add the following line to the end of main:  
    
`std::cout<<i<<std::endl;`
