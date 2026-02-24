# #45 - Double precision from_chars parsing incorrectly on reused buffer [Closed]

> Username: mborland  
> Created at: 2023-05-18 14:56:40 UTC  
> Updated at: 2023-05-22 14:41:28 UTC  
> Closed at: 2023-05-22 14:41:28 UTC  
> Url: https://github.com/boostorg/charconv/issues/45  

Examples from the benchmark PR (test_boost_to_chars with chars_format::general):  
  
```  
Roundtrip failure with: -1042963531082612  
          to_chars val: -1042963531082612.5  
          to_chars ptr: 19  
        from_chars val: -1.042963531082612e+164  
        from_chars ptr: 24  
Roundtrip failure with: -2984127144195026  
          to_chars val: -2984127144195026.5  
          to_chars ptr: 19  
        from_chars val: -2.984127144195027e-233  
        from_chars ptr: 24  
Roundtrip failure with: 1816265025891540  
          to_chars val: 1816265025891539.5  
          to_chars ptr: 18  
        from_chars val: 1.81626502589154e-98  
        from_chars ptr: 24  
Roundtrip failure with: 217945471321669.8  
          to_chars val: 217945471321669.75  
          to_chars ptr: 18  
        from_chars val: 0  
        from_chars ptr: 24  
```  
  
Single precision does not have any issues.
