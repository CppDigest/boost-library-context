# #54 - template argument list is expected after a name prefixed by the template keyword [Closed]

> Username: dcooley  
> Created at: 2024-09-18 22:45:37 UTC  
> Updated at: 2024-09-20 17:31:21 UTC  
> Closed at: 2024-09-20 17:31:21 UTC  
> Url: https://github.com/boostorg/qvm/issues/54  

According to [this llvm](https://github.com/llvm/llvm-project/issues/101079) issue, the `template` keyword should be followed by an argument list.   
  
There are a few instances where this is not the case, e.g.  
  
https://github.com/boostorg/qvm/blob/develop/include/boost/qvm/quat_traits.hpp#L62 (and L45 72, 82, 92 in that file)  
  
```c++  
write_quat_element( Q & q, typename quat_traits<Q>::scalar_type s )  
    {  
    quat_traits<Q>::template write_element<I>(q) = s;  
    }  
```  
  
The conversation in the LLVM issue suggests [the solution](https://github.com/llvm/llvm-project/issues/101079#issuecomment-2257270087) is to remove the `template` keyword  
  
> Clang is correctly rejecting the boost code. The template keyword must be followed by a template-id (i.e. a name followed by template argument list), except when the name refers to a class or alias template (and this use is deprecated).  
  
> From a quick look at the code, the template keyword simply should be removed.  
  
  
e.g: (untested)  
  
```c++  
write_quat_element( Q & q, typename quat_traits<Q>::scalar_type s )  
    {  
    quat_traits<Q>::write_element<I>(q) = s;  
    }  
```

---

## Comment 1

> Username: zajo  
> Created at: 2024-09-19 23:09:02 UTC  
> Url: https://github.com/boostorg/qvm/issues/54#issuecomment-2362352142  

Yes, this is a bug, however you had me confused because you linked the wrong function template. The error is in write_quat_element_idx. Thanks for reporting, will fix.

---

## Comment 2

> Username: dcooley  
> Created at: 2024-09-19 23:17:55 UTC  
> Url: https://github.com/boostorg/qvm/issues/54#issuecomment-2362360294  

apologies, I thought they all had the issue. But thanks for looking into it.

---

## Comment 3

> Username: zajo  
> Created at: 2024-09-20 17:31:21 UTC  
> Url: https://github.com/boostorg/qvm/issues/54#issuecomment-2364193671  

Merged in develop.
