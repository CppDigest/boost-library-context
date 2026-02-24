# #89 - constexpr uuid from string [Closed]

> Username: tobias-loew  
> Created at: 2018-12-21 15:07:32 UTC  
> Updated at: 2025-09-09 13:53:20 UTC  
> Closed at: 2025-09-09 13:53:20 UTC  
> Url: https://github.com/boostorg/uuid/issues/89  

Hi,  
while searching for constexpr GUID from string generators, I found https://gist.github.com/AlexBAV/b58e92d7632bae5d6f5947be455f796f  
which inspired me to generate a C++11 consexpr version plus boost-uuid generators.  
If you're interested, please have a look at https://github.com/tobias-loew/constexpr-GUID-cpp-11  
  
Tobias

---

## Comment 1

> Username: jeking3  
> Created at: 2019-05-14 10:40:00 UTC  
> Url: https://github.com/boostorg/uuid/issues/89#issuecomment-492184814  

It's an interesting concept, although my assumption is the vast majority of uuid-from-string comes from variable input at runtime, not compile-time?

---

## Comment 2

> Username: tobias-loew  
> Created at: 2019-05-14 11:19:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/89#issuecomment-492195983  

My use-case are e.g. windows CLSIDs and other fixed uuid that are statically compiled into the program. Here you can get the benefits of consistency checks at compile-time.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-05-14 11:34:32 UTC  
> Url: https://github.com/boostorg/uuid/issues/89#issuecomment-492200363  

Okay that makes sense.  Thanks.

---

## Comment 4

> Username: HO-COOH  
> Created at: 2025-07-19 18:37:13 UTC  
> Url: https://github.com/boostorg/uuid/issues/89#issuecomment-3092505250  

Better just make a constexpr constructor for uuid that takes a string, something like:  
```cpp  
# if !defined(BOOST_NO_CXX14_CONSTEXPR)  
    constexpr  
#endif  
    uuid(char const (&str)[37])  
    {  
        if (str[8] != '-' || str[13] != '-' || str[18] != '-' || str[23] != '-') {  
            throw "Invalid uuid format: hyphens not in the expected positions";  
        }  
  
        auto charToHex = [](char c)  
        {  
            return (c >= '0' && c <= '9') ? static_cast<uint8_t>(c - '0') :  
                (c >= 'a' && c <= 'f') ? static_cast<uint8_t>(10 + (c - 'a')) :  
                (c >= 'A' && c <= 'F') ? static_cast<uint8_t>(10 + (c - 'A')) :  
                throw "Invalid hex character";  
        };  
  
        int hex_indices[32] = {  
            0,1,2,3,4,5,6,7,  
            9,10,11,12,  
            14,15,16,17,  
            19,20,21,22,  
            24,25,26,27,28,29,30,31,32,33,34,35  
        };  
  
        for (int i = 0; i < 16; ++i)   
        {  
            auto const high_nibble = charToHex(str[hex_indices[2 * i]]);  
            auto const low_nibble = charToHex(str[hex_indices[2 * i + 1]]);  
            data[i] = (high_nibble << 4) | low_nibble;  
        }  
    }  
```  
The thing is it duplicate quite a lot of code with the existing string generator.

---

## Comment 5

> Username: pdimov  
> Created at: 2025-08-17 08:15:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/89#issuecomment-3194219054  

I'll look into making the existing string generator constexpr, but this would probably require C++17.  
  
The entire `uuid` class isn't constexpr-friendly at the moment because its operations (such as op==) use SIMD intrinsics and other tricks for performance. To fix this we'll need `std::is_constant_evaluated`, but that's even C++20.

---

## Comment 6

> Username: pdimov  
> Created at: 2025-09-01 01:57:53 UTC  
> Url: https://github.com/boostorg/uuid/issues/89#issuecomment-3240648908  

`string_generator` is now `constexpr` on develop.
