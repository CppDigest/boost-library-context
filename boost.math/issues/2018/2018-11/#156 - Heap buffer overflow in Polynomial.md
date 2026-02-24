# #156 - Heap buffer overflow in Polynomial: [Open]

> Username: NAThompson  
> Created at: 2018-11-14 19:37:53 UTC  
> Updated at: 2021-07-16 07:08:38 UTC  
> Url: https://github.com/boostorg/math/issues/156  

Code to reproduce:  
  
```cpp  
#include <iostream>  
#include <vector>  
#include <boost/math/tools/polynomial.hpp>  
  
using boost::math::tools::polynomial;  
  
template<class Real>  
void problem(size_t N)  
{  
    std::vector<Real> coeffs(N);  
    for (size_t i = 0; i < coeffs.size(); ++i) {  
      coeffs[i] = i;  
    }  
  
    polynomial<Real> p(coeffs.data(), coeffs.size());  
  
    std::cout << "Polynomial: " << p << std::endl;  
}  
  
int main()  
{  
    problem<double>(2);  
}  
```  
  
Compile with AddressSanitizer:  
  
```bash  
$ clang++ --std=gnu++17 -O0 -g -fsanitize=address -fsanitize=undefined -Wall -Wfatal-errors -march=native main.cpp  
```  
  
Then:  
  
```bash  
=================================================================  
==90840==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x602000000120 at pc 0x00010b72a581 bp 0x7ffee44eef90 sp 0x7ffee44eef88  
READ of size 8 at 0x602000000120 thread T0  
    #0 0x10b72a580 in std::__1::enable_if<__is_forward_iterator<double const*>::value, void>::type std::__1::vector<double, std::__1::allocator<double> >::__construct_at_end<double const*>(double const*, double const*, unsigned long) vector:1030  
    #1 0x10b729a4d in std::__1::vector<double, std::__1::allocator<double> >::vector<double const*>(double const*, std::__1::enable_if<(__is_forward_iterator<double const*>::value) && (is_constructible<double, std::__1::iterator_traits<double const*>::reference>::value), double const*>::type) vector:1178  
    #2 0x10b726644 in std::__1::vector<double, std::__1::allocator<double> >::vector<double const*>(double const*, std::__1::enable_if<(__is_forward_iterator<double const*>::value) && (is_constructible<double, std::__1::iterator_traits<double const*>::reference>::value), double const*>::type) vector:1170  
    #3 0x10b72656d in boost::math::tools::polynomial<double>::polynomial<double>(double const*, unsigned int) polynomial.hpp:292  
    #4 0x10b71e300 in boost::math::tools::polynomial<double>::polynomial<double>(double const*, unsigned int) polynomial.hpp:293  
    #5 0x10b711c00 in void problem<double>(unsigned long) main.cpp:21  
    #6 0x10b7114b7 in main main.cpp:28  
    #7 0x7fff6fc2b08c in start (libdyld.dylib:x86_64+0x1708c)  
```  
  
The constructor call *should* be `polynomial<double> p(coeffs.data(), coeffs.size() -1)`, but this doesn't seem idiomatic to me. Is there any way we can assert on this?

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-01-22 17:21:24 UTC  
> Url: https://github.com/boostorg/math/issues/156#issuecomment-456485315  

I propose we put the `[[deprecated("Please use the move constructor instead")]]` tag on this constructor which would help both performance and help avoid the segfault.

---

## Comment 2

> Username: Akaame  
> Created at: 2021-07-04 22:42:32 UTC  
> Url: https://github.com/boostorg/math/issues/156#issuecomment-873676452  

```cpp  
template <class U>  
   polynomial(const U* data, unsigned order)  
      : m_data(data, data + order + 1)  
   {  
       normalize();  
   }  
  ```  
    
  The target constructor does not seem so intuitive. The interface only makes sense when I open the source and see that _order_ of the polynomial is requested here. If I somehow knew that the second parameter is order, then using  
    
  ```cpp  
  polynomial<double> p(coeffs.data(), coeffs.size() - 1)  
  ```  
    
  is quite natural for the consumer/caller of this constructor IMHO.  
    
  What I would consider idiomatic(for better of a better wording, read "programmers are used to it") is to pass a raw pointer and a corresponding length. In that case,  
    
  ```cpp  
template <class U>  
   polynomial(const U* data, unsigned offset)  
      : m_data(data, data + offset)  
   {  
       normalize();  
   }  
  ```  
    
  seem to be making more sense for the general public but nonetheless as error-prone as the other approach.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-07-04 23:06:17 UTC  
> Url: https://github.com/boostorg/math/issues/156#issuecomment-873680006  

Agreed; I think it's not idiomatic.  
  
Adding the deprecation would move this to C++-14 compatibility only though; I don't think it's possible to change the current behavior.  
  
@jzmaddock : Would a C++-11 break to recommend the move constructor be ok for this one?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-07-05 18:24:45 UTC  
> Url: https://github.com/boostorg/math/issues/156#issuecomment-874271951  

>The interface only makes sense when I open the source and see that order of the polynomial is requested here. If I somehow knew that the second parameter is order, then using `polynomial<double> p(coeffs.data(), coeffs.size() - 1)` is quite natural for the consumer/caller of this constructor IMHO.  
  
Well... that parameter is clearly labelled as "order" in the docs, though I confess the class is otherwise rather under-documented :(  
  
>Adding the deprecation would move this to C++-14 compatibility only though; I don't think it's possible to change the current behavior.  
  
>@jzmaddock : Would a C++-11 break to recommend the move constructor be ok for this one?  
  
That function has always been that way, so I kind of hesitate to deprecate it.  
  
However, adding a deprecate attribute when in C++14 would do not harm, even if it was just to emphasise the point that the second parameter is polynomial order not number of terms.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2021-07-16 07:08:38 UTC  
> Url: https://github.com/boostorg/math/issues/156#issuecomment-881228143  

I agree with @NAThompson that it's not an intuitive interface, since a constructor for a container-esque class with that signature is a pretty widely established convention to mean (first, size).  
I know it would be an API break to change the meaning of that constructor now... but I wonder... would you get more problems by breaking it or not breaking it? Yes, if we break it, existing users will be annoyed and have to change their code, but that's a finite, non-increasing number of people. On the other hand, there is an 'infinite' number of people that will start using it, and be surprised by this constructor, and potentially open bug reports like this one.  :)  
If we don't break it, we'll have to keep explaining it and warning people about it.  
I'm ruthless, I would break it for the good of the future and warn the existing users, with a generous timeline of warning messages, etc.  
But that's easy for me to say, I won't be the one receiving the hate mail for breaking it.
