# #257 - Compilation error with MSVC 2022 [Closed]

> Username: mglisse  
> Created at: 2024-06-18 21:07:31 UTC  
> Updated at: 2024-07-13 07:07:04 UTC  
> Closed at: 2024-07-13 07:07:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/257  

```c++  
#include <boost/unordered/unordered_flat_set.hpp>  
struct A  
{  
    struct H { size_t operator()(const int* c) const; };  
    using C = boost::unordered_flat_set<int*, H>;  
    using I = C::iterator;  
};  
int main(){}  
```  
Compiling this with Visual Studio 2022 (17.8.5) and Boost 1.85 yields  
```pre  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2247: 'A::H' not accessible because 'boost::empty_::empty_value<Hash,0,true>' uses 'private' to inherit from 'A::H'  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2247:         with  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2247:         [  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2247:             Hash=A::H  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2247:         ]  
1>(compiling source file 'ConsoleApplication1.cpp')  
1>C:\Users\ci\source\repos\ConsoleApplication1\ConsoleApplication1\ConsoleApplication1.cpp(4,12):  
1>see declaration of 'A::H'  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\core.hpp(1361,12):  
1>see declaration of 'boost::empty_::empty_value<Hash,0,true>'  
1>        with  
1>        [  
1>            Hash=A::H  
1>        ]  
1>C:\Users\ci\source\repos\ConsoleApplication1\ConsoleApplication1\ConsoleApplication1.cpp(4,12):  
1>see declaration of 'A::H'  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32):  
1>the template instantiation context (the oldest one first) is  
1>	C:\Users\ci\source\repos\ConsoleApplication1\ConsoleApplication1\ConsoleApplication1.cpp(6,26):  
1>	see reference to class template instantiation 'boost::unordered::unordered_flat_set<int *,A::H,std::equal_to<Key>,std::allocator<Key>>' being compiled  
1>        with  
1>        [  
1>            Key=int *  
1>        ]  
1>	C:\vcpkg\installed\x64-windows\include\boost\unordered\unordered_flat_set.hpp(48,18):  
1>	see reference to class template instantiation 'boost::unordered::detail::foa::table<boost::unordered::detail::foa::flat_set_types<Key>,Hash,KeyEqual,std::allocator<Key>>' being compiled  
1>        with  
1>        [  
1>            Key=int *,  
1>            Hash=A::H,  
1>            KeyEqual=std::equal_to<int *>  
1>        ]  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2248: 'A::H': cannot access private struct declared in class 'boost::empty_::empty_value<Hash,0,true>'  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2248:         with  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2248:         [  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2248:             Hash=A::H  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\table.hpp(348,32): error C2248:         ]  
1>(compiling source file 'ConsoleApplication1.cpp')  
1>C:\Users\ci\source\repos\ConsoleApplication1\ConsoleApplication1\ConsoleApplication1.cpp(4,12):  
1>see declaration of 'A::H'  
1>C:\vcpkg\installed\x64-windows\include\boost\unordered\detail\foa\core.hpp(1361,12):  
1>see declaration of 'boost::empty_::empty_value<Hash,0,true>'  
1>        with  
1>        [  
1>            Hash=A::H  
1>        ]  
```  
This is most likely a bug in visual studio (it might even be fixed already in more recent versions, I don't have other versions at hand right now), but in case you want to work around it in Boost somehow...

---

## Comment 1

> Username: k3DW  
> Created at: 2024-06-19 01:39:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/257#issuecomment-2177360128  

It looks like this isn't specifically an issue in Unordered. I reproduced this using only a stand-in for `boost::empty_value`. It's still happening on the latest Visual Studio 2022, 17.10.3. The Unordered code does basically the following.  
```cpp  
// A stand-in for boost::empty_value  
template <class T>  
class empty_value : T  
{  
public:  
    using type = T;  
};  
  
template <class T>  
struct S : empty_value<T>  
{  
    using type = typename empty_value<T>::type;  
};  
  
struct Outer { struct Inner {}; };  
  
S<Outer::Inner> s{};  
  
int main(){}  
```  
This clearly looks like a compiler bug in MSVC. It could be fixed by inheriting publicly from `T` in `empty_value`. I'm not sure if this is a breaking change in Core. There is no workaround I could see in the Unordered library. I'll look into changing this in Core.  
  
For your use case, you can work around this problem by moving `H` outside of `A`. It seems like MSVC has a problem with nested classes/structs in this case.

---

## Comment 2

> Username: joaquintides  
> Created at: 2024-07-12 15:44:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/257#issuecomment-2225856612  

Hi @mglisse can you confirm if [this fix](https://github.com/boostorg/core/pull/175) solves your original problem? Thank you!

---

## Comment 3

> Username: mglisse  
> Created at: 2024-07-12 22:50:22 UTC  
> Url: https://github.com/boostorg/unordered/issues/257#issuecomment-2226469062  

Thanks. I'll try to check if it fixes the original problem at some point, but don't hold your breath (I don't have the environment available anymore). If it fixes the reduced testcase, I think that's good enough to close the issue, and I'll open a new one later if it turns out not to be sufficient for our full code.
