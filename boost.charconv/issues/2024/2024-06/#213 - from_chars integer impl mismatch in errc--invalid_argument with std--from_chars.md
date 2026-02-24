# #213 - from_chars integer impl mismatch in errc::invalid_argument with std::from_chars [Closed]

> Username: deepak1556  
> Created at: 2024-06-28 16:25:59 UTC  
> Updated at: 2024-06-28 19:50:53 UTC  
> Closed at: 2024-06-28 19:50:52 UTC  
> Url: https://github.com/boostorg/charconv/issues/213  

For the following example,  
  
```c++  
uint16_t segment_result{};  
std::string_view input{"/c"};  
auto r = std::from_chars(input.data(), input.data() + input.size(),  
                        segment_result);  
```  
  
std::from_chars results in `std::errc::invalid_argument` while the boost implementation does not error - https://godbolt.org/z/WT7nPjeoK

---

## Comment 1

> Username: mborland  
> Created at: 2024-06-28 17:10:40 UTC  
> Url: https://github.com/boostorg/charconv/issues/213#issuecomment-2197346168  

This definetly is wrong from the boost side. Will take a look at this one too. Thanks again for clear bug reports.
