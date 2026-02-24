# #199 - missing/misused m_is_wide in char_wchar_holder assignment operators [Closed]

> Username: nobuddhy  
> Created at: 2023-07-18 08:23:38 UTC  
> Updated at: 2024-09-26 21:57:08 UTC  
> Closed at: 2024-09-26 21:55:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/199  

`m_is_wide` is not set in these both operators  
so if wideness has changed it might lead to call wrong `delete[]` later  
  
https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/char_wchar_holder.hpp#L54-L61  
  
https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/char_wchar_holder.hpp#L63-L70  
  
`if` condition should be opposite  
currently it always assigns null string  
  
https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/char_wchar_holder.hpp#L72-L79  
  
https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/char_wchar_holder.hpp#L86-L90

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-26 21:57:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/199#issuecomment-2378009175  

Many thanks for the report and sorry for the long wait.
