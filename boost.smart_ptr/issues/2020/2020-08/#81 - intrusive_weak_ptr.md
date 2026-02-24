# #81 - intrusive_weak_ptr [Open]

> Username: strimo378  
> Created at: 2020-08-03 07:55:51 UTC  
> Updated at: 2023-07-10 21:05:14 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/81  

Hi everybody,  
  
I have implemented a weak version of intrusive_ptr. The basic idea is that the destructor call and memory deallocation is performed independently. Destructor is called if use_count = 0 and deallocation in case of weak_count = 0.  
  
Please find attached the first implementation of instrucive_weak_ptr and the corresponding intrusive_weak_ref_counter. The current implementation is without any modifications of the original smart_ptr source code and does not support all compilers/architectures.  
  
I have tested the implementation on gcc 8.3 and VS 2019 with an internal compiler project with heavy usage of smart pointers within the AST.  
  
Please let me know if the intrusive_weak_ptr implementation could be integration into the boost library. In that case, I would love to finalize it for an official integration.  
  
Kind regards,  
  
Timo Stripf  
[intrusive_weak_ptr.zip](https://github.com/boostorg/smart_ptr/files/5014609/intrusive_weak_ptr.zip)

---

## Comment 1

> Username: pdimov  
> Created at: 2020-10-24 13:07:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/81#issuecomment-715912547  

Interesting idea, but `~Derived` also destroys `intrusive_weak_ref_counter`, and accessing `m_weak_counter` after that is undefined behavior.  
  
This can be hacked around in single-threaded by constructing an `intrusive_weak_ref_counter` at the same place, with the same counter values, but it doesn't work if another thread increments `m_weak_counter` at the same time.  
  
One way to do this by the book would be to not destroy the object and instead call a virtual function in the derived class, say, `intrusive_ptr_destroy`, that would be responsible for releasing resources.

---

## Comment 2

> Username: strimo378  
> Created at: 2020-11-02 22:34:48 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/81#issuecomment-720762989  

I see your point that it is problematic to use objects after their destructor has been called. I would specify it as an requirement that the destructor call is not changing/resetting the weak counter value. Fundamental types have no default destructor that could change their value.  
  
Can you tell me the C++ standard paragraph that defines accessing m_weak_counter as undefined behaviour? I found several possible paragraphs but I am unsure which really defines it.  
  
I think the hack would also work for multi-threaded code if a constructor is called that is doing nothing. Reusing storage is afaik allowed. A constructor and destructor of intrusive_weak_ref_counter that is doing nothing would pratically work but in theory if another thread is accessing the pointer value between both calls would be undefined :)  
  
I like the idea of intrusive_ptr_destroy and maybe a intrusive_ptr_delete function.

---

## Comment 3

> Username: hadrielk  
> Created at: 2023-07-10 21:05:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/81#issuecomment-1629730972  

> Can you tell me the C++ standard paragraph that defines accessing m_weak_counter as undefined behaviour?  
  
Section 15.7 of C++17 spec:  
  
> For an object with a non-trivial destructor, referring to any non-static member or base class of the object after the destructor finishes execution results in undefined behavior.  
  
Since the intrusive weak-ref-count would be in the base class of some derived class T, it could not be accessed after the destruction of T without being undefined behavior.
