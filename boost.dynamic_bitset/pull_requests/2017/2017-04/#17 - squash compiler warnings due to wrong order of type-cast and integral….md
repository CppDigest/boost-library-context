# #17 squash compiler warnings due to wrong order of type-cast and integral… [Merged]

> Username: DanielaE  
> Created at: 2017-04-16 16:04:57 UTC  
> Updated at: 2017-12-29 17:51:38 UTC  
> Merged at: 2017-06-27 13:36:10 UTC  
> Closed at: 2017-06-27 13:36:10 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17  

… type promotion  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-16 16:07:55 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-294359620  

These narrowing warnings are seen with all msvc compilers (10 ... 14.1) in my tests at warning level 4 (the same as targeted by Microsoft's standard library).

---

## Comment 2

> Username: mclow  
> Created_at: 2017-05-04 00:51:34 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-299074620  

Does MSVC 2017 warn on this too?

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-05-04 08:59:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-299131094  

Yes, it does:  
```  
compile-c-c++ Z:\boost\bin.v2\libs\dynamic_bitset\test\dyn_bitset_unit_tests2.test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dyn_bitset_unit_tests2.obj  
dyn_bitset_unit_tests2.cpp  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.10.25017\include\xutility(2690,1): warning C4244: '=': conversion from 'const int' to 'unsigned char', possible loss of data  
		*_First = _Val;  
^  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.10.25017\include\xutility(2704): note: see reference to function template instantiation 'void std::_Fill_unchecked1<_FwdIt,_Ty>(_FwdIt,_FwdIt,const _Ty &,std::false_type)' being compiled  
        with  
        [  
            _FwdIt=unsigned char *,  
            _Ty=int  
        ]  
	_Fill_unchecked1(_First, _Last, _Val, _Fill_memset_is_safe(_First, _Val));  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.10.25017\include\xutility(2712): note: see reference to function template instantiation 'void std::_Fill_unchecked<unsigned char*,_Ty>(_FwdIt,_FwdIt,const _Ty &)' being compiled  
        with  
        [  
            _Ty=int,  
            _FwdIt=unsigned char *  
        ]  
	_Fill_unchecked(_Unchecked(_First), _Unchecked(_Last), _Val);  
E:\Boost\Boost.org\boost/dynamic_bitset/dynamic_bitset.hpp(976): note: see reference to function template instantiation 'void std::fill<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<unsigned char>>>,int>(_FwdIt,_FwdIt,const _Ty &)' being compiled  
        with  
        [  
            _FwdIt=std::_Vector_iterator<std::_Vector_val<std::_Simple_types<unsigned char>>>,  
            _Ty=int  
        ]  
  std::fill(m_bits.begin(), m_bits.end(), ~Block(0));  
```  
```  
compile-c-c++ Z:\boost\bin.v2\libs\dynamic_bitset\test\dyn_bitset_unit_tests3.test\msvc-14.1\dbg\adrs-mdl-64\thrd-mlt\dyn_bitset_unit_tests3.obj  
dyn_bitset_unit_tests3.cpp  
E:\Boost\Boost.org\boost/dynamic_bitset/dynamic_bitset.hpp(1055,1): warning C4245: 'initializing': conversion from 'int' to 'unsigned char', signed/unsigned mismatch  
    block_type const all_ones = ~static_cast<Block>(0);  
^  
E:\Boost\Boost.org\boost/dynamic_bitset/dynamic_bitset.hpp(1049): note: while compiling class template member function 'bool boost::dynamic_bitset<Block,std::allocator<Block>>::all(void) const'  
        with  
        [  
            Block=unsigned char  
        ]  
```

---

## Comment 4

> Username: mclow  
> Created_at: 2017-05-24 19:31:40 UTC  
> Updated_at: 2017-05-24 19:31:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-303827822  

That looks .. verbose to me. Instead of `static_cast<Block>(~(Block(0)));`, how about `Block(~0)` or `static_cast<Block>(~0);` Does either of those suppress the warning?

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-05-24 19:38:12 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-303829427  

I would prefer not to encourage C-style casts in C++ code. So static_cast<Block>(~0); seems OK to me but not Block(~0).

---

## Comment 6

> Username: mclow  
> Created_at: 2017-05-24 19:41:02 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-303830118  

BTW, I spoke to a MS compiler engineer, and he agreed that the compiler was wrong in warning on this code.

---

## Comment 7

> Username: DanielaE  
> Created_at: 2017-05-25 08:46:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/17#issuecomment-303957388  

Ok guys, your wish is my command - the PR is reworked accordingly.  
  
@mclow , I've checked this warning thing in Godbolt's Compiler Explorer with latest msvc, clang and gcc at equivalent warning levels using these four lines of code:  
```  
int main() {  
  unsigned char x = ~0;  
  unsigned long long y = ~0;  
  x = ~0u;  
  y = ~0u;  
}  
```  
msvc /W4 gives:  
```  
<source>(2): warning C4245: 'initializing': conversion from 'int' to 'unsigned char', signed/unsigned mismatch  
<source>(3): warning C4245: 'initializing': conversion from 'int' to 'unsigned __int64', signed/unsigned mismatch  
<source>(4): warning C4305: '=': truncation from 'unsigned int' to 'unsigned char'  
<source>(4): warning C4309: '=': truncation of constant value  
```  
clang -Wconversion tells us:  
```  
<source>:2:21: warning: implicit conversion changes signedness: 'int' to 'unsigned char' [-Wsign-conversion]  
  unsigned char x = ~0;  
                ~   ^~  
<source>:3:26: warning: implicit conversion changes signedness: 'int' to 'unsigned long long' [-Wsign-conversion]  
  unsigned long long y = ~0;  
                     ~   ^~  
<source>:4:7: warning: implicit conversion from 'unsigned int' to 'unsigned char' changes value from 4294967295 to 255 [-Wconstant-conversion]  
  x = ~0u;  
    ~ ^~~  
```  
gcc -Wconversion utters:  
```  
<source>:4:7: warning: large integer implicitly truncated to unsigned type [-Woverflow]  
   x = ~0u;  
       ^~~  
```  
So, msvc and clang agree on the code smells and point them out identically, whereas gcc is mostly ignorant.

---
