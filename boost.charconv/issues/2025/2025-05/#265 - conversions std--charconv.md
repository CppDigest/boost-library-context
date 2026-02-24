# #265 - conversions std::charconv [Open]

> Username: gpeterhoff  
> Created at: 2025-05-23 22:01:14 UTC  
> Updated at: 2025-05-23 22:01:14 UTC  
> Url: https://github.com/boostorg/charconv/issues/265  

It would be very helpful to have conversion contruructors/conversion operators directly in from_chars_result/to_chars_result. You use boost/core/detail/string_view yourself, as the compatibility is provided.  
A customized cast is required for chars_format.  
  
Is the template from_chars_result_t really necessary?  
  
[chars_format.hpp.txt](https://github.com/user-attachments/files/20418608/chars_format.hpp.txt)  
[from_chars_result.hpp.txt](https://github.com/user-attachments/files/20418610/from_chars_result.hpp.txt)  
[to_chars_result.hpp.txt](https://github.com/user-attachments/files/20418611/to_chars_result.hpp.txt)  
  
regards  
Gero
