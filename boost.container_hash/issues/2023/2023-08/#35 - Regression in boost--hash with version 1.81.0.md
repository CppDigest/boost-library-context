# #35 - Regression in boost::hash with version 1.81.0 [Open]

> Username: kkumar45  
> Created at: 2023-08-18 10:57:02 UTC  
> Updated at: 2023-08-24 03:35:07 UTC  
> Url: https://github.com/boostorg/container_hash/issues/35  

We are noticing regression with `boost::hash` when used with `std::u16string` in boost 1.81.0 version and compared to 1.78.0. As per the 1.81.0 documentation, it seems there are major changes in `boost::hash` and the performance with string should be improved. Any thoughts on this regression?  
  
Sample program -   
```  
#include <boost/functional/hash.hpp>  
#include <iostream>  
#include <chrono>  
  
int main()  
{  
    // Start the timer  
    auto start = std::chrono::high_resolution_clock::now();  
  
    for(int i = 0; i < 10; i++)  
    {  
        boost::hash<std::u16string>()(u"hello");  
    }  
  
    // End the timer  
    auto end = std::chrono::high_resolution_clock::now();  
  
    // Calculate the duration  
    std::chrono::duration<double> duration = end - start;  
  
    // Print the duration in seconds  
    std::cout << "Execution time: " << (duration.count())/10 << " seconds." << std::endl;  
  
    return 0;  
}  
```  
  
Link 1.78.0 - https://godbolt.org/z/ManWPoK8j  
Link 1.81.0 - https://godbolt.org/z/hejbsfhc6

---

## Comment 1

> Username: pdimov  
> Created at: 2023-08-23 21:12:26 UTC  
> Url: https://github.com/boostorg/container_hash/issues/35#issuecomment-1690645596  

String performance is indeed improved, but only for narrow strings (e.g. `std::string`.) For `char16_t`, the generic sequence hash is still used.  
  
The inner loop in 1.71 has three multiplications, one shift and two xors  
  
```  
.L9:  
        movzx   eax, WORD PTR [rdx]  
        add     rdx, 2  
        imul    rax, r15  
        mov     r9, rax  
        shr     r9, 47  
        xor     rax, r9  
        imul    rax, r15  
        xor     rax, r8  
        imul    rax, r15  
        lea     r8, [rax+r13]  
        cmp     rsi, rdx  
        jne     .L9  
```  
whereas the one in 1.81 has two multiplications, three shifts and three xors  
```  
.L9:  
        movzx   r8d, WORD PTR [rdx]  
        add     rdx, 2  
        add     r8, r13  
        add     r8, rax  
        mov     rax, r8  
        shr     rax, 32  
        xor     rax, r8  
        imul    rax, r15  
        mov     r8, rax  
        shr     r8, 32  
        xor     rax, r8  
        imul    rax, r15  
        mov     r8, rax  
        shr     r8, 28  
        xor     rax, r8  
        cmp     rsi, rdx  
        jne     .L9  
```  
Which one is better is highly dependent on the specific CPU architecture. It looks like the latest x86 CPUs have a very fast multiplication, so the 1.78 implementation wins; but, if it's any consolation, the newer one is more theoretically sound and has better hash quality.

---

## Comment 2

> Username: kkumar45  
> Created at: 2023-08-24 03:35:07 UTC  
> Url: https://github.com/boostorg/container_hash/issues/35#issuecomment-1690942623  

@pdimov Are there any plan to address it, anytime soon?
