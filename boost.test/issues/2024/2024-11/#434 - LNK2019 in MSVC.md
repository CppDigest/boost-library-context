# #434 - LNK2019 in MSVC [Closed]

> Username: Fireshtorm1k  
> Created at: 2024-11-27 12:52:54 UTC  
> Updated at: 2024-11-27 14:07:04 UTC  
> Closed at: 2024-11-27 14:07:03 UTC  
> Url: https://github.com/boostorg/test/issues/434  

Trying to do a simple test program.  
  
```cpp  
#define BOOST_TEST_MODULE Test  
#include <iostream>  
  
BOOST_AUTO_TEST_CASE(FirstTest)  
{  
BOOST_REQUIRE_EQUAL(1,1);  
}  
```  
  
When compiling im getting:  
  
`libboost_unit_test_framework-vc143-mt-gd-x64-1_86.lib(unit_test_parameters.obj) : error LNK2019: unresolved external symbol __std_mismatch_1 in function "unsigned __int64 __cdecl std::_Mismatch_vectorized<1>(void const * const,void const * const,unsigned __int64)" (??$_Mismatch_vectorized@$00@std@@YA_KQEBX0_K@Z).`  
  
Using boost-1.86 with VS2022

---

## Comment 1

> Username: Fireshtorm1k  
> Created at: 2024-11-27 14:07:03 UTC  
> Url: https://github.com/boostorg/test/issues/434#issuecomment-2503970201  

Resolved by update Visual Studio to version 17.12.2
