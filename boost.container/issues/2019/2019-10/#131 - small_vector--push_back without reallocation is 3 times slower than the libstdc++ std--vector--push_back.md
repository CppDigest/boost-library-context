# #131 - small_vector::push_back without reallocation is 3 times slower than the libstdc++ std::vector::push_back [Closed]

> Username: apolukhin  
> Created at: 2019-10-02 12:55:01 UTC  
> Updated at: 2020-11-02 13:59:06 UTC  
> Closed at: 2020-11-02 13:59:05 UTC  
> Url: https://github.com/boostorg/container/issues/131  

Compilers inline the `priv_forward_range_insert_no_capacity` function and that function touches a lot of local variables. The compilers are forced to free up some registers and they push data on the stack.  
  
As a result here's how the small_vector::push_back looks like:  
```  
test_boost(boost::container::small_vector<int, 8ul, void, void>&):  
  push r15  
  push r14  
  push r13  
  push r12  
  push rbp  
  push rbx  
  mov rbx, rdi  
  sub rsp, 8  
  mov rax, QWORD PTR [rdi+8]  
  mov rdx, QWORD PTR [rdi]  
  mov rcx, QWORD PTR [rdi+16]  
  lea rbp, [rdx+rax*4]  
  cmp rax, rcx  
  jnb .L7  
  add rax, 1  
  mov DWORD PTR [rbp+0], 42  
  mov QWORD PTR [rdi+8], rax  
.L6:  
  add rsp, 8  
  pop rbx  
  pop rbp  
  pop r12  
  pop r13  
  pop r14  
  pop r15  
  ret  
```  
  
While the `std::vector::push_back` looks like the following:  
```  
test_std(std::vector<int, std::allocator<int> >&):  
  sub rsp, 24  
  mov rsi, QWORD PTR [rdi+8]  
  mov DWORD PTR [rsp+12], 42  
  cmp rsi, QWORD PTR [rdi+16]  
  je .L20  
  mov DWORD PTR [rsi], 42  
  add rsi, 4  
  mov QWORD PTR [rdi+8], rsi  
  add rsp, 24  
  ret  
```  
Godbolt playground: https://godbolt.org/z/CvRqMY  
  
Note that adding `BOOST_NOINLINE` to `priv_forward_range_insert_no_capacity` does not help.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-10-19 10:26:24 UTC  
> Url: https://github.com/boostorg/container/issues/131#issuecomment-544125054  

I can't see how could this should be solved., the inliner takes the wrong decision. Which compiler and version?

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-02-20 21:05:04 UTC  
> Url: https://github.com/boostorg/container/issues/131#issuecomment-589313056  

Reported the issues here https://gcc.gnu.org/bugzilla/show_bug.cgi?id=91981 and here https://bugs.llvm.org/show_bug.cgi?id=43562

---

## Comment 3

> Username: apolukhin  
> Created at: 2020-02-20 21:05:37 UTC  
> Url: https://github.com/boostorg/container/issues/131#issuecomment-589313822  

GCC and Clang of almost any version

---

## Comment 4

> Username: igaztanaga  
> Created at: 2020-11-02 13:59:05 UTC  
> Url: https://github.com/boostorg/container/issues/131#issuecomment-720488573  

Since it looks like an inliner issue, I'm closing this. thanks for the report.
