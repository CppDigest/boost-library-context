# #209 - chars in char_set<punct_chars> not sorted [Closed]

> Username: hfinkel  
> Created at: 2024-12-23 06:44:15 UTC  
> Updated at: 2024-12-23 23:30:36 UTC  
> Closed at: 2024-12-23 23:28:53 UTC  
> Url: https://github.com/boostorg/parser/issues/209  

In include/boost/parser/detail/unicode_char_sets.hpp, the chars in char_set<punct_chars> are not sorted; to excerpt:  
  
```cpp  
    template<>  
    struct char_set<punct_chars>  
    {  
        static constexpr uint32_t chars[] = {  
            0x21,    0x22,    0x23,    0x25,    0x26,    0x27,    0x28,  
            0x29,    0x2A,    0x5B,    0x5C,    0x5D,    0x5F,    0x7B,  
            0x7D,    0x2C,    0x2D,    0x2E,    0x2F,    0x3A,    0x3B,  
            0x3F,    0x40,    0xA1,    0xA7,    0xAB,    0xB6,    0xB7,  
            0xBB,    0xBF,    0x37E,   0x387,   0x55A,   0x55B,   0x55C,  
            0x55D,   0x55E,   0x55F,   0x589,   0x58A,   0x5BE,   0x5C0,  
            0x5C3,   0x5C6,   0x5F3,   0x5F4,   0x609,   0x60A,   0x60C,  
            0x60D,   0x61B,   0x61D,   0x61E,   0x61F,   0x66A,   0x66B,  
            0x66C,   0x66D,   0x6D4,   0x700,   0x701,   0x702,   0x703,  
    ...  
```  
And because 0x2c (',') comes after 0x7d, and so on, char_set_parser, which uses std::upper_bound does not match ',' and other characters that are in the array.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-12-23 23:30:35 UTC  
> Url: https://github.com/boostorg/parser/issues/209#issuecomment-2560428315  

Oof.  What a stupid mistake.  Thanks for reporting this, Hal!  I usually take big sets of code points like this and write a little program to sort them.  It looks like I did this for N-1 of the `char_set`s.  This is now fixed on develop, if you want to use that until the next Boost release.
