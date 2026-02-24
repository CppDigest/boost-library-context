# #24 - BOOST_DLL_ALIAS does not export symbol on MinGW-w64 [Closed]

> Username: markand  
> Created at: 2018-11-09 10:27:34 UTC  
> Updated at: 2018-11-11 21:41:19 UTC  
> Closed at: 2018-11-10 14:54:16 UTC  
> Url: https://github.com/boostorg/dll/issues/24  

On MinGW-w64, the BOOST_DLL_ALIAS does not export symbol. I presume this is because the macro  only declares avariable and GCC strips it.  
  
```cpp  
#define BOOST_DLL_ALIAS_SECTIONED(FunctionOrVar, AliasName, SectionName)                        \  
    namespace _autoaliases {                                                                    \  
        extern "C" BOOST_SYMBOL_EXPORT const void *AliasName;                                   \  
    } /* namespace _autoaliases */                                                              \  
    /**/  
```  
  
If I try to do manually the same in a source file. It confirms my thoughts:  
  
**Using a defined function**  
  
```cpp  
extern "C" {  
  
__declspec(dllexport)  
int my_super_mario_function()  
{  
    return 0;  
}  
  
}  
```  
  
Once built, the symbol appears correctly:  
  
```  
$ g++ -std=c++17 -shared -o plugin.dll plugin.cpp -fvisibility=hidden -lboost_system-mt && nm plugin.dll | grep my_  
0000000067e8142d t _GLOBAL__sub_I__ZN6custom11my_functionEv  
0000000067e813b0 T _ZN6custom11my_functionEv  
0000000067e813cb T my_super_mario_function  
```  
  
**Using a simple declaration**  
  
```cpp  
extern "C" {  
  
__declspec(dllexport)  
int my_super_mario_function();  
  
}  
```  
  
In that case, no symbol is exported.  
  
```  
$ g++ -std=c++17 -shared -o plugin.dll plugin.cpp -fvisibility=hidden -lboost_system-mt && nm plugin.dll | grep my_  
0000000067e81422 t _GLOBAL__sub_I__ZN6custom11my_functionEv  
0000000067e813b0 T _ZN6custom11my_functionEv  
```  
  
However, assigning a value seems to work, but a bit ugly.  
  
```cpp  
namespace custom {  
  
void my_function()  
{  
        puts("CALLED");  
}  
  
} // !custom  
  
extern "C" {  
  
__declspec(dllexport) void* my_super_function = reinterpret_cast<void*>(&(custom::my_function));  
  
}  
```  
  
The symbol is there:  
  
```  
$ g++ -std=c++17 -shared -o plugin.dll plugin.cpp -fvisibility=hidden -lboost_system-mt && nm plugin.dll | grep my_  
0000000067e81422 t _GLOBAL__sub_I__ZN6custom11my_functionEv  
0000000067e813b0 T _ZN6custom11my_functionEv  
0000000067e85010 D my_super_function  
```  
  
What can we do to fix that issue? Is there a compile flag that export declared symbol?

---

## Comment 1

> Username: markand  
> Created at: 2018-11-09 10:36:20 UTC  
> Updated at: 2018-11-11 08:10:30 UTC  
> Url: https://github.com/boostorg/dll/issues/24#issuecomment-437319480  

Okay, just realized there was the [Limitations](https://www.boost.org/doc/libs/1_68_0/doc/html/boost_dll/limitations.html) part that explains this.  
  
Note to myself: always read the whole documentation before asking.  
Proposal: make this limitation section part of "Getting started" chapter.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-11-10 14:54:16 UTC  
> Url: https://github.com/boostorg/dll/issues/24#issuecomment-437589300  

That won't help :(  
The topic is quite complex, the FAQ and Misuses chapters may be also very important to start using the library.

---

## Comment 3

> Username: markand  
> Created at: 2018-11-11 08:12:42 UTC  
> Updated at: 2018-11-11 08:13:30 UTC  
> Url: https://github.com/boostorg/dll/issues/24#issuecomment-437651442  

By the way, why make this option and not the behaviour under BOOST_DLL_FORCE_ALIAS_INSTANTIATION macro the default? That would solve this problem easily. Also, I do think that a library should *work* immediately without any user option to be set. That is, the option BOOST_DLL_FORCE_ALIAS_INSTANTIATION should be the opposite (like BOOST_DLL_FORCE_WEAK_SYMBOLS or something similar). What do you think?

---

## Comment 4

> Username: apolukhin  
> Created at: 2018-11-11 21:41:19 UTC  
> Url: https://github.com/boostorg/dll/issues/24#issuecomment-437707586  

Did not fully get the idea, but sounds like a breaking change
