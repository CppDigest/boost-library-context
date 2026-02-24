# #212 - from_chars integer impl mismatch in errc::result_out_of_range results with std::from_chars [Closed]

> Username: deepak1556  
> Created at: 2024-06-28 15:18:17 UTC  
> Updated at: 2024-07-01 08:06:32 UTC  
> Closed at: 2024-06-28 19:50:38 UTC  
> Url: https://github.com/boostorg/charconv/issues/212  

For the following example,  
  
```c++  
boost::charconv::from_chars_result r;  
uint32_t segment_result{};  
std::string_view input{"0xffffffff1"};  
r = boost::charconv::from_chars(input.data() + 2, input.data() + input.size(),  
                        segment_result, 16);  
```  
  
std implementation - https://godbolt.org/z/1ascnPYbY results in `std::errc::result_out_of_range`  
boost implementation - https://godbolt.org/z/o6aej3qP7 results in wrapped around value  
  
Current implementation says overflow should not be possible in [first nd characters](https://github.com/boostorg/charconv/blob/e77744b300d53c4b6f41c17ac8a2c41ad04c3533/include/boost/charconv/detail/from_chars_integer_impl.hpp#L200-L213) but seems that is possible https://godbolt.org/z/Mb5d39obv ?

---

## Comment 1

> Username: mborland  
> Created at: 2024-06-28 17:09:02 UTC  
> Url: https://github.com/boostorg/charconv/issues/212#issuecomment-2197343319  

> For the following example,  
>   
> ```c++  
> boost::charconv::from_chars_result r;  
> uint32_t segment_result{};  
> std::string_view input{"0xffffffff1"};  
> r = boost::charconv::from_chars(input.data() + 2, input.data() + input.size(),  
>                         segment_result, 16);  
> ```  
>   
> std implementation - https://godbolt.org/z/1ascnPYbY results in `std::errc::result_out_of_range` boost implementation - https://godbolt.org/z/o6aej3qP7 results in wrapped around value  
>   
> Current implementation says overflow should not be possible in [first nd characters](https://github.com/boostorg/charconv/blob/e77744b300d53c4b6f41c17ac8a2c41ad04c3533/include/boost/charconv/detail/from_chars_integer_impl.hpp#L200-L213) but seems that is possible https://godbolt.org/z/Mb5d39obv ?  
  
It looks like `nd` has a builtin assumption about base 10 number so base 16 will cause overflow as shown. Will investigate. Thanks for the comprehensive report.

---

## Comment 2

> Username: deepak1556  
> Created at: 2024-07-01 08:06:30 UTC  
> Url: https://github.com/boostorg/charconv/issues/212#issuecomment-2199504535  

Thanks for the quick turnaround!
