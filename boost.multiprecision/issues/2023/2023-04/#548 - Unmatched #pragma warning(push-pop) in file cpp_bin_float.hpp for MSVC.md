# #548 - Unmatched #pragma warning(push/pop) in file cpp_bin_float.hpp for MSVC [Closed]

> Username: VasilyevAlexander  
> Created at: 2023-04-13 15:24:37 UTC  
> Updated at: 2023-04-13 15:30:19 UTC  
> Closed at: 2023-04-13 15:30:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/548  

File cpp_bin_float.hpp, boost version 1.81.  
  
There are  
1. #pragma warning(push) in line 54, and matched #pragma warning(pop) in line 620.  
2. #pragma warning(push) in line 1119, and matched #pragma warning(pop) in line 1276.  
3. #pragma warning(push) in line 1294, and matched #pragma warning(pop) in line 1400.  
4. #pragma warning(push) in line 2079, and matched #pragma warning(pop) in line 2108.  
  
And unmatched #pragma warning(pop) in line 2150.

---

## Comment 1

> Username: mborland  
> Created at: 2023-04-13 15:30:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/548#issuecomment-1507175809  

This was fixed with: https://github.com/boostorg/multiprecision/commit/3f42566a7d5ab4d7d9ed81613fa61609e2084e07 and will be in Boost version 1.82
