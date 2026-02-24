# #37 - shared.hpp does not compile in nvidia CUDA [Closed]

> Username: ghost  
> Created at: 2021-02-02 19:40:08 UTC  
> Updated at: 2021-02-16 01:51:53 UTC  
> Closed at: 2021-02-15 18:35:32 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37  

The nvcc compiler does not compile include\boost\preprocessor\slot\detail version 1.75.0. Compile fails with "C1012 unmatched parenthesis: missing ')'" on line 27  
![image](https://user-images.githubusercontent.com/35534426/106653245-ea7f0b80-656c-11eb-9c25-0fe5cdbeae5f.png)

---

## Comment 1

> Username: eldiener  
> Created at: 2021-02-03 06:33:16 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772270689  

I can not read whatever you have above. I need more information before I can look at your problem.

---

## Comment 2

> Username: ghost  
> Created at: 2021-02-03 13:02:29 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772490909  

@eldiener my apologies I have attached a self contained visual studio project that produces the error. You will need to run the following vcpkg install command to acquire the dependencies:  
  
./vcpkg install boost:x64-windows-static  
  
I'm working from vcpkg commit: d97ce7dfc24939519c93978524766967fb5d95aa  
  
Which results in boost version 1.75.0  
  
[BoostPreprocessorHeader.zip](https://github.com/boostorg/preprocessor/files/5918570/BoostPreprocessorHeader.zip)  
.

---

## Comment 3

> Username: eldiener  
> Created at: 2021-02-03 16:35:09 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772644382  

I need a command line that produces an error. I have no interest in running vcpkg etc.

---

## Comment 4

> Username: eldiener  
> Created at: 2021-02-03 19:04:37 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772745783  

To reiterate. I need code, I need a command line, and I need to know what compiler you are using and where I can get it if I do not already have it. Simply saying "I have a problem with the preprocessor library. Solve it" is not enough for me to look at your problem.

---

## Comment 5

> Username: ghost  
> Created at: 2021-02-03 19:36:09 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772768460  

The following code snipped does not compile in the NVCC compiler from CUDA 11.0 or CUDA 11.2. The code does compile in the MSVC compiler v142 so it could be an issue with the Nvidia compiler/preprocessor. The compiler fails in the file 'shared.hpp' somewhere on the include tree stemming from the include of "signal.hpp". Specifically the compiler fails with "C1012	unmatched parenthesis: missing ')' on line 27 of 'shared.hpp'.  
  
#include <boost/signals2/signal.hpp>  
  
// TODO: This is an example of a library function  
void fnBoostPreprocessorHeader()  
{  
}  
  
Hopefully that gives you some more information.

---

## Comment 6

> Username: eldiener  
> Created at: 2021-02-03 19:42:43 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772772820  

I have no idea what `fnBoostPreprocessorHeader()` is supposed to be. Are you saying that just including the signals2 header file gives the error above ?   
  
If so, what compiler and what command line, and why are you creating an issue here rather then under signals2 ?  
  
If not you actually have to produce code which shows the error, the compiler you are using and the command line parameters for that compiler.

---

## Comment 7

> Username: ghost  
> Created at: 2021-02-03 19:47:11 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772775460  

I'm saying if you compile the code exactly as typed in my comment above with the nvcc compiler in CUDA 11 it will give you the error I'm describing.  
  
So yes the inclusion of signals2 will produce the error with no code beyond what I typed above.

---

## Comment 8

> Username: eldiener  
> Created at: 2021-02-03 19:53:38 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772779319  

Please report the problem to signals2 https://github.com/boostorg/signals2 . I suggest you give them the exact compiler used and a command line. If you still think it is a preprocessor problem you need to show me actual preprocessor code which is causing the problem for the nvcc compiler, as well as a command line I can use to reproduce the problem.

---

## Comment 9

> Username: ghost  
> Created at: 2021-02-03 19:55:01 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-772780060  

Ok I will send it their way thanks! :)

---

## Comment 10

> Username: ghost  
> Created at: 2021-02-10 12:56:13 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-776687627  

boostorg/signals2#53

---

## Comment 11

> Username: mobereng  
> Created at: 2021-02-15 18:28:51 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779389044  

I will join this issue, I ran into the same compilation problem:  
  
![image](https://user-images.githubusercontent.com/69315094/107982247-0f767400-6f9a-11eb-8964-b844ef19450f.png)  
  
CUDA: 11.2  
Boost: 1.75.0

---

## Comment 12

> Username: ghost  
> Created at: 2021-02-15 18:35:32 UTC  
> Updated at: 2021-02-15 18:38:14 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779391633  

@mobereng This was resolved in boostorg/signals2#53. The preprocessor dev branch has this fix. This issue has been resolved. There should be regression testing for boost using the NVCC compiler. NVCC is currently committed from the test suite.

---

## Comment 13

> Username: mobereng  
> Created at: 2021-02-15 18:38:25 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779392616  

@dahubley Amazing, let me change to development branch

---

## Comment 14

> Username: mobereng  
> Created at: 2021-02-15 20:54:46 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779443482  

@dahubley It worked, thanks for the help !

---

## Comment 15

> Username: eldiener  
> Created at: 2021-02-15 21:15:32 UTC  
> Updated at: 2021-02-15 21:16:10 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779450827  

It would be nice if there were Boost regression tests for the nvcc compiler, but I have never seen any. I think this is because there has never been a Boost Build toolset for nvcc.

---

## Comment 16

> Username: ghost  
> Created at: 2021-02-15 21:32:48 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779457046  

@eldiener if boost is making NVCC specific changes then it seems important to have builds on NVCC completed before release. Otherwise it's pretty hard to know things will work on that compiler. I would do this myself if our company had the resources but I work for a 5 person shop. I'm hoping someone will see value in the idea and move it forward.

---

## Comment 17

> Username: eldiener  
> Created at: 2021-02-16 01:26:49 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779525181  

Go to Boost Build and try to convince them there to have a Boost Build toolset for nvcc. Then someone needs to provide either Boost regression tests for the nvcc compiler, or some CI testing service, appveyor, travis etc., needs to have the ability of testing nvcc.

---

## Comment 18

> Username: ghost  
> Created at: 2021-02-16 01:29:02 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779525763  

@ericniebler thanks I will do that!

---

## Comment 19

> Username: ghost  
> Created at: 2021-02-16 01:51:53 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/37#issuecomment-779531304  

@eldiener @eldiener I have added a feature request to boost build.
