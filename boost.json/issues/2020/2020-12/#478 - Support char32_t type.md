# #478 - Support char32_t type [Closed]

> Username: ghost  
> Created at: 2020-12-16 10:27:55 UTC  
> Updated at: 2020-12-23 17:40:45 UTC  
> Closed at: 2020-12-23 17:40:45 UTC  
> Url: https://github.com/boostorg/json/issues/478  

Hello dear developers.   
  
I guess this message is kind of wish, because my ecosystem based on char32_t and I don't want to operate with char type at all (as the result of it I don't want to have sort of legacy like casting functions and etc).  
  
And how to use _parse_ or _parser_ or _stream_parser_ instances if I have only char32_t without any casting operation, library doesn't provide this functionality?   
  
It will be cool if we have boost::json::parser<char_based_type> parser; parser.write(your_stl_basic_string<char_based_type>); // or something like this idk   
  
(Boost version 1.75.0 | C++20 | cmake 3.19.1 | gcc 10.2.0)

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-23 11:05:58 UTC  
> Url: https://github.com/boostorg/json/issues/478#issuecomment-750150385  

?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-12-23 14:28:46 UTC  
> Url: https://github.com/boostorg/json/issues/478#issuecomment-750324078  

Hmmm, no I don't see this happening. If you want to parse a JSON in 32-bit chars, convert it to normal 8-bit `char` first. Make sure it is UTF-8 encoded (if you have any code points outside the range 1..127).

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-23 17:40:45 UTC  
> Url: https://github.com/boostorg/json/issues/478#issuecomment-750403787  

> Hmmm, no I don't see this happening. If you want to parse a JSON in 32-bit chars, convert it to normal 8-bit `char` first. Make sure it is UTF-8 encoded (if you have any code points outside the range 1..127).  
  
Okay thank you for reply.
