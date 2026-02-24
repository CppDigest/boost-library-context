# #121 - conversion warning on MSVC (VS 2022 17.9.1, toolset v143, std=C++latest) [Closed]

> Username: tobias-loew  
> Created at: 2024-02-27 13:38:06 UTC  
> Updated at: 2024-03-15 21:29:31 UTC  
> Closed at: 2024-03-15 21:29:31 UTC  
> Url: https://github.com/boostorg/parser/issues/121  

in file include\boost\parser\detail\text\transcode_iterator.hpp, line 3184  
  
```  
                auto it = encode_code_point(cp, buf_.begin());  
                buf_last_ = uint8_t(it - buf_.begin());  
                buf_index_ = buf_last_ - 1;  
                to_increment_ = std::distance(curr(), initial);  
                             ^^^  
```  
  
line 3194 seems also affected
