# #297 - Binary string to cpp_int [Open]

> Username: aaangeletakis  
> Created at: 2021-02-11 17:40:47 UTC  
> Updated at: 2025-09-29 18:03:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297  

Hello, I noticed boost multiprecision does not have a binary string to decimal converter, so I made one. Hope you enjoy!  
```c++  
// credit: Alexios A Angel  
// C++ program to convert binary to decimal  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
/******************************************************  
 * Function : cpp_int bin_2_dec(const std::string_view& num)  
 * Input    : A string of ones and zeros  
 * Output   : cpp_int  
 ******************************************************  
 * Read digits left to right  
 *  Ex.  
 *                 MSB            LSB  
 *               { '1', '1', '1', '0' }  
 *   str index:     0    1    2    3  
 *   bin index      3    2    1    0  
 *  
 *   The bit index is inverse (displayed above) to the  
 *   digits in the string, so the algorithm will be  
 *  
 *   ┌──┬───────────────────────────────────────────────────────┐  
 *   │1 │unsigned int bin_2_dec(const std::string_view& str)    │  
 *   │2 │{                                                      │  
 *   │3 │   unsigned int dec_value = 0;                         │  
 *   │4 │   int          len       = str.size();                │  
 *   │5 │   int          bin_index = len - 1;                   │  
 *   │6 │   int          str_index = 0;                         │  
 *   │7 │                                                       │  
 *   │8 │   for(; str_index < len; ++str_index, --bin_index){   │  
 *   │9 │      dec_value |= (str[str_index] - '0') << bin_index;│  
 *   │10│   }                                                   │  
 *   │11│   return dec_value;                                   │  
 *   │12│}                                                      │  
 *   └──┴───────────────────────────────────────────────────────┘  
 *   You could say we are converting Little endian to Big endian.   
 *   Unfortunately, if there are more than sizeof(int) digits in the string than the shift in Line 9  
 *   will overflow. In order to get around this in boost mp we   
 *   use boost::multiprecision::bit_set  
 */  
  
//change std::string_view to std::span<const char> if  
//number of digits is greater than std::string_view{}.max_size()  
boost::multiprecision::cpp_int bin_2_dec(const std::string_view& num)  
{  
  
    boost::multiprecision::cpp_int dec_value = 0;  
    auto cptr = num.data();  
    auto len  = num.size();  
    //check if big enough to have 0b postfix  
    if(num.size() > 2){  
        //check if 2nd character is the 'b' binary postfix  
        //skip over it & adjust length accordingly if it is  
        if(num[1] == 'b' || num[1] == 'B'){  
            cptr += 2;  
            len  -= 2;  
        }  
    }  
  
    //change i's type to cpp_int if the number of digits is greater  
    //than std::numeric_limits<size_t>::max()  
    for (size_t i = len - 1; 0 <= i; ++cptr, --i) {  
        if(*cptr == '1'){  
            boost::multiprecision::bit_set(/*.val = */ dec_value, /*.pos = */ i);  
        }  
    }  
    return dec_value;  
}  
  
// Driver program to test above function  
int main()  
{  
                                  //266 bits  
    std::cout << bin_2_dec(/*.num = */"0b11111111111111111111111111111111111111111111111111111111111111111"  
                                      "1111111111111111111111111111111111111111111111111111111111111111111"  
                                      "1111111111111111111111111111111111111111111111111111111111111111111"  
                                      "1111111111111111111111111111111111111111111111111111111111111111111") << '\n';  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-02-23 22:03:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-784545164  

This is an interesting post. Boost.Multiprecision is transitioning to C++11 for the next release. Literal binary strings, if memory serves, arrived in C++14. Boost.Multiprecision is, however, not strictly tied to that exact language evolutionary step.  
  
Does auto-detect of binary-string-to-multiprecision type make sense? And if so, for which (or all) or our backends?  
Any interest in digit separators, or is this a separate issue altogether?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-24 10:37:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-784981041  

There are really two features here:  
  
* iostream support for binary strings (which isn't in C++14 in fact, but no matter).  
* Literal/constexpr support via user defined literals.  
  
The former is valid for all integer backends, the latter only for cpp_int, and need some hairy-scary modification to the existing user-defined-literal code ('tis horrible to write).  
  
For iostream code, it should really be possible to write it in backend-agnostic format.  
  
While we're at it, we should support ' separators in numbers as well.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-02-24 19:15:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-785310831  

> There are really two features here  
  
Indeed.  
This seems like a larger operation. Points have been noted for potential future handling of this issue.

---

## Comment 4

> Username: aaangeletakis  
> Created at: 2021-02-25 18:39:10 UTC  
> Updated at: 2021-02-25 21:00:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-786116529  

I would suggest it be based on std::bitset as it covers both of those requirements @jzmaddock .  
```c++  
#include <iostream>  
#include <bitset>  
  
int main(void)  
{  
    constexpr unsigned int myNum = 3;  
    constexpr std::bitset< sizeof(myNum) * 8 > bits(myNum);  
    std::cout << bits << std::endl;  
    return 0;  
}  
//Compiled with -std=c++11  
//Output: 00000000000000000000000000000011  
```  
  
As for arbitrary size literals, we can base it off of [boost::dynamic_bitset](https://www.boost.org/doc/libs/1_75_0/libs/dynamic_bitset/dynamic_bitset.html)  
```c++  
#include <iostream>  
#include <boost/dynamic_bitset.hpp>  
  
int main(void)  
{  
    unsigned int myNum = 3;  
    boost::dynamic_bitset<> bits(sizeof(myNum) * 8, myNum);  
    std::cout << bits << std::endl;  
    return 0;  
}  
```  
https://godbolt.org/z/PK3dxY

---

## Comment 5

> Username: aaangeletakis  
> Created at: 2021-02-26 00:27:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-786323114  

Forgive my handwriting and spelling, but I believe an std::bin for binary strings support would look something like this:  
  
  
![Image of Yaktocat](https://i.imgur.com/iOQx3s9.jpg)

---

## Comment 6

> Username: everdrone  
> Created at: 2023-06-18 13:57:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-1596156768  

Would love to have the octal `0o123` as well! thanks for the code!

---

## Comment 7

> Username: jzmaddock  
> Created at: 2023-06-19 11:21:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-1597006964  

>Would love to have the octal 0o123 as well! thanks for the code!  
  
Octal has always been supported, but it's 0123, there's no "o" letter in C++ octal strings.

---

## Comment 8

> Username: alan23273850  
> Created at: 2024-11-14 14:43:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-2476533938  

Thanks for @aaangeletakis solution! One thing to note, the compiler warns me that "comparison of unsigned expression in ‘>= 0’ is always true." Maybe we can change the types of `len` and `i` to `long long`.  
  
>     for (size_t i = len - 1; 0 <= i; ++cptr, --i) {

---

## Comment 9

> Username: vinipsmaker  
> Created at: 2025-09-29 18:03:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/297#issuecomment-3348325284  

> > Would love to have the octal 0o123 as well! thanks for the code!  
>   
> Octal has always been supported, but it's 0123, there's no "o" letter in C++ octal strings.  
  
Outside Boost.Multiprecision code, we can skip (`remove_prefix(2)`) the initial two characters to have only the actual number part. Then pass what's left of the string to Boost.Multiprecision. Unfortunately Boost.Multiprecision doesn't allow one to specify the base of the input string (as in `std::from_chars()`) so this won't work.  
  
Can we have an overloaded constructor to accept the base of the input string (as in `std::from_chars()`)? I need to convert from binary, octal and hexadecimal strings. Right now the only thing that works is hexadecimal, but only because (by accident) Boost.Multiprecision chose the same prefix as I do to represent hexadecimal number strings.
