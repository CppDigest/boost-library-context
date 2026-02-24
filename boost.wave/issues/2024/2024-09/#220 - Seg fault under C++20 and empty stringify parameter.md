# #220 - Seg fault under C++20 and empty stringify parameter. [Closed]

> Username: jwnhy  
> Created at: 2024-09-11 02:59:52 UTC  
> Updated at: 2024-09-15 04:42:34 UTC  
> Closed at: 2024-09-15 04:42:34 UTC  
> Url: https://github.com/boostorg/wave/issues/220  

Tested on the development branch.  
  
```C  
#define t(x, ...) #x#__VA_ARGS__  
int main()  
{  
  t(1);  
}  
```  
  
Triggers a segment fault when Wave tries to stringify the empty string; the check [here](https://github.com/boostorg/wave/blob/7ce8b53d9dc56a9c0b9a8855f26003e6b174301f/include/boost/wave/util/cpp_macromap.hpp#L1266) does nothing as `i` is off-the-bound.  
  
I think I will PR to fix this...

---

## Comment 1

> Username: jwnhy  
> Created at: 2024-09-11 04:05:23 UTC  
> Url: https://github.com/boostorg/wave/issues/220#issuecomment-2342576283  

The funny thing is that this bug does not surface if there is only one parameter, a.k.a `#define t(...)` with or even without C++ 20 (Yes, Wave does not complain about too few arguments either).  
  
This is a separate issue, another Issue and PR should be opened.
