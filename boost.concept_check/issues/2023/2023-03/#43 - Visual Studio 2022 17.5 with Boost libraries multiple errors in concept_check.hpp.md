# #43 - Visual Studio 2022 17.5 with Boost libraries multiple errors in concept_check.hpp [Open]

> Username: PCzachoA  
> Created at: 2023-03-15 12:14:37 UTC  
> Updated at: 2023-03-16 09:47:06 UTC  
> Url: https://github.com/boostorg/concept_check/issues/43  

After VS 2022 update from 17.4.xx to 17.5 I started to get multiple errors detected in concept_check.hpp module.  
  
While my whole code is compiling and build properly, the Intellisense of VS2022 stops working due to many errors detected in editor.  
  
The issue is confirmed on Microsoft VS 2022 community by few users however not solution so far and not sure where the problems is.  
  
Problem is not visible in VS2022 17.4.xx and ealier.  
  
The problem is confirmed in latest Boost 1.81 and ealier 1.78.  
![VS2022_17 5_BOOST](https://user-images.githubusercontent.com/127952176/225305961-2dbbeddc-c9ac-47ec-ae79-b0270f55bd7b.PNG)

---

## Comment 1

> Username: wudiyidashi  
> Created at: 2023-03-16 09:47:05 UTC  
> Url: https://github.com/boostorg/concept_check/issues/43#issuecomment-1471624478  

my version is Microsoft VS 2022 community 17.5.0  
Try this issue, it works  for me.  
[issue #42 ](https://github.com/boostorg/concept_check/issues/42#issuecomment-1454202666)
