# #29 - [compile error] #if #endif pairs did not match in boost\assert.hpp [Open]

> Username: Bzz2745  
> Created at: 2022-10-04 17:01:19 UTC  
> Updated at: 2022-10-26 14:09:53 UTC  
> Url: https://github.com/boostorg/assert/issues/29  

When I tried to compile PCL(PointCloudLibrary) with the Boost file compiled by myself, the error showed that #if #endif pairs did not match and the error was reported in the assert.hpp file from Boost, so I checked the file in VS2022 and didn't see any error prompt. I have compared the latest version of Boost compiled files, and every line is the same. (Because it is necessary to compile part of the code with NVCC, NVCC always prompts that the *.hpp file of Boost cannot be found. Even though I have completely set it in path and CMake, I changed the include directory to a pure English absolute path, but I think this should not be the reason. ) I hope to get an answer, thank you very much!  
My platform: Windows11  
Compiler: vs2022 (or vc143) & nvcc (from cuda 11.6)

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-04 17:08:37 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267306414  

What's the actual error you are getting?

---

## Comment 2

> Username: Bzz2745  
> Created at: 2022-10-04 17:09:28 UTC  
> Updated at: 2022-10-04 17:09:44 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267307312  

One more thing,this error didn't appear when I build with VS2019(VC142) and nvcc from CUDA Toolkit11.4

---

## Comment 3

> Username: pdimov  
> Created at: 2022-10-04 17:15:34 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267313737  

Can you please copy and paste the error, or screenshot it?

---

## Comment 4

> Username: Bzz2745  
> Created at: 2022-10-04 17:16:23 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267314650  

> What's the actual error you are getting?  
  
just #if and #endif not match no more thing

---

## Comment 5

> Username: Bzz2745  
> Created at: 2022-10-04 17:20:52 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267319612  

> Can you please copy and paste the error, or screenshot it?  
  
OK please wait 5min

---

## Comment 6

> Username: Bzz2745  
> Created at: 2022-10-04 17:25:05 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267324349  

>   
  
![image](https://user-images.githubusercontent.com/68807654/193885735-ad07e9f5-a5d6-4884-91ec-b5346d92a7ad.png)

---

## Comment 7

> Username: Bzz2745  
> Created at: 2022-10-04 17:26:52 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267326304  

> >   
>   
> ![image](https://user-images.githubusercontent.com/68807654/193885735-ad07e9f5-a5d6-4884-91ec-b5346d92a7ad.png)  
  
the code just is assert.hpp

---

## Comment 8

> Username: pdimov  
> Created at: 2022-10-04 17:38:47 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267339735  

I've no idea why this would happen, sorry. It's easy to see that all `#if`s in `assert.hpp` are matched. I took a quick look at PCL's repo, but nothing jumped out at me; it doesn't even include `boost/assert.hpp` anywhere directly.

---

## Comment 9

> Username: Bzz2745  
> Created at: 2022-10-04 17:48:56 UTC  
> Updated at: 2022-10-04 17:50:01 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1267350580  

> 我不知道为什么会发生这种情况，对不起。很容易看出所有都是匹配的。我快速浏览了一下PCL的存储库，但没有任何东西跳出来。它甚至不包括任何地方直接。`#if``assert.hpp``boost/assert.hpp`  
  
Yes,I've also tried to delete part of code,but the error still exist,maybe the reason is VS2022......and this is two questions actually,NVCC.exe always shows "can't open file 'assert.hpp',no aush file or directory" even I add boost to path.......Only by changing the include directory to a pure English absolute path can I solve this.....maybe they will be found and then fixed when PCL 1.13(if it use VC143 on windows) released.

---

## Comment 10

> Username: Bzz2745  
> Created at: 2022-10-26 14:07:31 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1292108813  

Now i have already built all parts of pcl,and ensure this error is caused by CUDA,if i build some parts don't use CUDA,it will not appear.but i have no way to solve it. The error  "can't open boost/*.hpp"also caused by use CUDA(or some settings is wrong.),which is also not solved.

---

## Comment 11

> Username: Bzz2745  
> Created at: 2022-10-26 14:09:53 UTC  
> Url: https://github.com/boostorg/assert/issues/29#issuecomment-1292112349  

> Now i have already built all parts of pcl,and ensure this error is caused by CUDA,if i build some parts don't use CUDA,it will not appear.but i have no way to solve it. The error  "can't open boost/*.hpp"also caused by use CUDA(or some settings is wrong.),which is also not solved.  
  
You can find the issue contributed by me in pcl
