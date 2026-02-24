# #183 Disable long double overloads on platforms with unsupported types [Merged]

> Username: mborland  
> Created at: 2024-04-17 07:40:47 UTC  
> Updated at: 2024-04-22 07:11:00 UTC  
> Merged at: 2024-04-22 07:10:56 UTC  
> Closed at: 2024-04-22 07:10:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/183  

Closes: #182   
  
On QEMU Fedora 39 all tests now pass with the ABI being either `__float128` or `__ibm128`. The latter having `long double` overloads disabled across the board.

---
