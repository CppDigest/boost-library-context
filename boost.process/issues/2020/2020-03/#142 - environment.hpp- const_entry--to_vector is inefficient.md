# #142 - environment.hpp: const_entry::to_vector is inefficient [Closed]

> Username: jonesmz  
> Created at: 2020-03-13 06:46:41 UTC  
> Updated at: 2020-05-21 07:27:23 UTC  
> Closed at: 2020-05-21 06:15:05 UTC  
> Url: https://github.com/boostorg/process/issues/142  

```cpp  
struct const_entry  
{  
    std::vector<string_type> to_vector() const  
    {  
        if (_data == nullptr)  
            return std::vector<string_type>();  
        std::vector<string_type> data;  
        auto str = string_type(_data);  
        struct splitter  
        {  
            bool operator()(wchar_t w) const {return w == api::env_seperator<wchar_t>();}  
            bool operator()(char c)    const {return c == api::env_seperator<char>   ();}  
        } s;  
        boost::split(data, _data, s);  
        return data;  
    }  
};  
```  
  
This algorithm is creating a std::string or std::wstring, incurring an allocation and copy, and then another allocation and copy for each sub-string split from the main string.  
  
At the very least, the variable "auto str" should be converted to a type that doesn't require an allocation, such as std::string_view.  
  
Further, is it really necessary to return a vector with std::strings instead of std::string_view? If std::string_view was used, no allocations or copies would happen.  
  
If there are some uses of the function that require ownership semantics, and other uses that don't, two versions of the functions could be provided.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-05-21 06:15:05 UTC  
> Url: https://github.com/boostorg/process/issues/142#issuecomment-631904373  

The library is still on  C++11.

---

## Comment 2

> Username: jonesmz  
> Created at: 2020-05-21 07:27:23 UTC  
> Url: https://github.com/boostorg/process/issues/142#issuecomment-631931427  

Then use boost::string_view. It compiles on C++11.  
  
It's not appropriate to leave such easy performance issues unfixed because of C++11.
