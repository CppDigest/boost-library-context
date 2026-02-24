# #120 - conversion warning on MSVC (VS 2022 17.9.1, toolset v143, std=C++latest) [Closed]

> Username: tobias-loew  
> Created at: 2024-02-27 13:02:41 UTC  
> Updated at: 2024-03-15 21:29:31 UTC  
> Closed at: 2024-03-15 21:29:31 UTC  
> Url: https://github.com/boostorg/parser/issues/120  

```  
    template<typename Tag>  
    struct char_set_parser  
    {  
        BOOST_PARSER_ALGO_CONSTEXPR char_set_parser()  
        {  
            auto const & chars = detail::char_set<Tag>::chars;  
            auto const first = std::begin(chars);  
            auto const last = std::end(chars);  
            auto it = std::upper_bound(first, last, 0x100);  
            if (it != last)  
                one_byte_offset_ = it - first;  
                                ^^^^  
````  
  
49>C:\Users\k8705963\Documents\GitHub\parser\include\boost\parser\parser.hpp(6180,39): warning C4244: '=': conversion from '__int64' to 'int', possible loss of data

---

## Comment 1

> Username: tobias-loew  
> Created at: 2024-02-27 13:34:38 UTC  
> Updated at: 2024-02-27 13:34:52 UTC  
> Url: https://github.com/boostorg/parser/issues/120#issuecomment-1966564730  

and two lines above there's another  
```  
auto it = std::upper_bound(first, last, 0x100);  
                                        ^^^^^  
```  
changing to `0x100u` removed the warning
