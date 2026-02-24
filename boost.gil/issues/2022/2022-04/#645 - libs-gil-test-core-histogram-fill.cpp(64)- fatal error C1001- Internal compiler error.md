# #645 - libs\gil\test\core\histogram\fill.cpp(64): fatal error C1001: Internal compiler error [Open]

> Username: mloskot  
> Created at: 2022-04-12 07:29:42 UTC  
> Updated at: 2023-07-16 06:15:53 UTC  
> Url: https://github.com/boostorg/gil/issues/645  

### Actual behavior  
  
Currently, this internal error is occurring for two CI jobs (recently improved by @striezel)  
  - windows (msvc-14.3, 14,17,latest, 32,64, windows-2022)  
  - windows (msvc-14.2, 14,17,latest, 32,64, windows-2019)  
  
Here is sample log:  
  
```  
2022-04-09T12:28:10.5312066Z compile-c-c++ bin.v2\libs\gil\test\core\histogram\fill.test\msvc-14.3\dbg\adrs-mdl-32\cxstd-ltst-iso\thrd-mlt\fill.obj  
2022-04-09T12:28:10.5520345Z fill.cpp  
2022-04-09T12:28:10.5555671Z libs\gil\test\core\histogram\fill.cpp(64): fatal error C1001: Internal compiler error.  
2022-04-09T12:28:10.5558643Z (compiler file 'msc1.cpp', line 1691)  
2022-04-09T12:28:10.5593791Z  To work around this problem, try simplifying or changing the program near the locations listed above.  
2022-04-09T12:28:10.6231214Z If possible please provide a repro here: https://developercommunity.visualstudio.com   
2022-04-09T12:28:10.6231947Z Please choose the Technical Support command on the Visual C++   
2022-04-09T12:28:10.6424482Z  Help menu, or open the Technical Support help file for more information  
2022-04-09T12:28:10.6481826Z C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\include\vector(1946): note: while evaluating constexpr function 'std::vector<std::_Vbase,std::allocator<std::_Vbase>>::data'  
2022-04-09T12:28:10.6509015Z C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\include\vector(2240): note: while evaluating constexpr function 'std::_Vb_iter_base<_Alvbase_wrapped>::_Total_off'  
2022-04-09T12:28:10.6552876Z         with  
2022-04-09T12:28:10.6602833Z         [  
2022-04-09T12:28:10.6608564Z             _Alvbase_wrapped=std::_Wrap_alloc<std::allocator<std::_Vbase>>  
2022-04-09T12:28:10.6620406Z         ]  
2022-04-09T12:28:10.6628742Z C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\include\xutility(4036): note: while evaluating constexpr function 'std::_Vb_iterator<std::_Wrap_alloc<std::allocator<std::_Vbase>>>::operator *'  
2022-04-09T12:28:10.6652164Z C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\include\vector(2816): note: while evaluating constexpr function 'std::_Copy_unchecked'  
2022-04-09T12:28:10.6688282Z C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\include\vector(2798): note: while evaluating constexpr function 'std::vector<bool,std::allocator<bool>>::_Insert'  
2022-04-09T12:28:10.6766846Z C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\include\vector(2529): note: while evaluating constexpr function 'std::vector<bool,std::allocator<bool>>::insert'  
2022-04-09T12:28:10.6819916Z INTERNAL COMPILER ERROR in 'C:\Program Files\Microsoft Visual Studio\2022\Enterprise\VC\Tools\MSVC\14.31.31103\bin\HostX86\x86\cl.exe'  
2022-04-09T12:28:12.0979146Z     Please choose the Technical Support command on the Visual C++  
2022-04-09T12:28:12.0979696Z     Help menu, or open the Technical Support help file for more information  
2022-04-09T12:28:12.0979977Z   
2022-04-09T12:28:12.0980652Z     call "bin.v2\standalone\msvc\msvc-14.3\adrs-mdl-32\archt-x86\msvc-setup.bat"  >nul  
2022-04-09T12:28:12.0987215Z  cl /Zm800 -nologo "libs\gil\test\core\histogram\fill.cpp" -c -Fo"bin.v2\libs\gil\test\core\histogram\fill.test\msvc-14.3\dbg\adrs-mdl-32\cxstd-ltst-iso\thrd-mlt\fill.obj"     -TP /wd4675 /EHs /std:c++latest /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline -DBOOST_ALL_NO_LIB=1 -DBOOST_GIL_USE_CONCEPT_CHECK=1 "-I." "-Ilibs\gil\test"   
2022-04-09T12:28:12.0987978Z   
2022-04-09T12:28:12.0988464Z ...failed compile-c-c++ bin.v2\libs\gil\test\core\histogram\fill.test\msvc-14.3\dbg\adrs-mdl-32\cxstd-ltst-iso\thrd-mlt\fill.obj...  
```  
  
Assuming `libs\gil\test\core\histogram\fill.cpp(64)` means line 64, bug must be coming from the vectors of `bool` here:  
https://github.com/boostorg/gil/blob/3e729e5dae4c0ba6f407f6885bf6a18d525e8489/test/core/histogram/fill.cpp#L58-L64  
  
@codejaeger since it's your code, before I jump in modifying it myself, would you have any suggestions/preferences?  
  
Some ideas:  
1. Replace ` std::vector<std::vector<bool>>` with  linear ` std::vector<bool>` or `std::bitset`  
2. Introduce `mask_2d` and `mask_2d_fixed` adapting linear `std::vector<bool>`, like `kernel_2d`  
  
### C++ Minimal Working Example  
  
```cpp  
// cl /std:c++latest /EHsc /MDd /D_DEBUG test.cpp  
#include <vector>  
  
std::vector<std::vector<bool>> mask =  
{  
    {1, 0, 0, 1},  
    {0, 0, 1, 1},  
    {0, 1, 0, 1},  
    {1, 1, 0, 0},  
};  
  
int main()  
{  
    return 0;  
}  
```  
  
![image](https://user-images.githubusercontent.com/80741/162906819-c6ddf6d9-407f-45ed-8c51-ce5747bc149e.png)

---

## Comment 1

> Username: sdebionne  
> Created at: 2022-04-12 08:16:48 UTC  
> Url: https://github.com/boostorg/gil/issues/645#issuecomment-1096331213  

I notice that only debug builds fail, aka `cl /std:c++latest /EHsc test.cpp` compiles.

---

## Comment 2

> Username: sdebionne  
> Created at: 2022-04-12 08:26:02 UTC  
> Url: https://github.com/boostorg/gil/issues/645#issuecomment-1096346270  

This bug report [Regression default-initializing static duration array of vectors in C++latest debug mode](https://developercommunity.visualstudio.com/t/Regression-default-initializing-static-d/1433426) is a close match.   
  
Which make me wonder why use `/std:c++latest`? Isn't this asking for troubles 😀 ?

---

## Comment 3

> Username: mloskot  
> Created at: 2022-04-12 10:18:18 UTC  
> Url: https://github.com/boostorg/gil/issues/645#issuecomment-1096514241  

FYI, I reported the issue here, https://developercommunity.visualstudio.com/t/INTERNAL-COMPILER-ERROR-compiling-std::v/10011526  
  
> Which make me wonder why use /std:c++latest? Isn't this asking for troubles 😀 ?  
  
Yes, `/std:c++latest` needs to be disabled.  
This is aligned with the grand plan as discussed in https://github.com/boostorg/gil/issues/642#issuecomment-1092730524

---

## Comment 4

> Username: mloskot  
> Created at: 2022-04-28 10:01:24 UTC  
> Url: https://github.com/boostorg/gil/issues/645#issuecomment-1112001454  

> FYI, I reported the issue here, https://developercommunity.visualstudio.com/t/INTERNAL-COMPILER-ERROR-compiling-std::v/10011526  
  
[Latest update](https://developercommunity.visualstudio.com/t/INTERNAL-COMPILER-ERROR-compiling-std::v/10011526#T-N10024311): _"A fix for this issue has been internally implemented and is being prepared for release. "_

---

## Comment 5

> Username: striezel  
> Created at: 2023-07-16 06:11:56 UTC  
> Updated at: 2023-07-16 06:15:53 UTC  
> Url: https://github.com/boostorg/gil/issues/645#issuecomment-1636991528  

According to https://developercommunity.visualstudio.com/t/INTERNAL-COMPILER-ERROR-compiling-std::v/10011526#T-N10277969 this should be fixed by now:  
  
> A fix for this issue has been released! Install the most recent release from https://visualstudio.microsoft.com/downloads/. Thank you for providing valuable feedback which has helped improve the product.  
  
Unfortunately, it does not say which versions of MSVC contain the fix. So somebody might still need to confirm that this is fixed for the versions used in the CI jobs.  
  
**Edit:** Oh wait ... it says _"Fixed In: Visual Studio 2022 version 17.3"_, so it should at least work for the `windows-2022` image in CI, because that is using VS 17.6.33815.320 at the moment. (See <https://github.com/actions/runner-images/blob/main/images/win/Windows2022-Readme.md#visual-studio-enterprise-2022>.)
