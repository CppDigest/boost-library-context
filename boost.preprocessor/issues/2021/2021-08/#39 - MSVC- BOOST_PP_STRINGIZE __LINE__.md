# #39 - MSVC: BOOST_PP_STRINGIZE __LINE__ [Open]

> Username: ohhmm  
> Created at: 2021-08-17 21:41:33 UTC  
> Updated at: 2021-08-25 15:22:00 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39  

Broken on MSVC:  
  
`BOOST_PP_STRINGIZE(__LINE__)` returns `"__LINE__"`

---

## Comment 1

> Username: eldiener  
> Created at: 2021-08-17 22:35:56 UTC  
> Updated at: 2021-08-18 01:14:36 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-900676317  

I am not seeing this in VS2019 with the latest Boost. What version of MSVC are you using and what version of Boost are you using ? My test program is:  
  
```  
# include <iostream>  
# include <boost/preprocessor/stringize.hpp>  
  
int main()   
	{  
	std::cout << '\n' << BOOST_PP_STRINGIZE(__LINE__) << '\n';  
        return 0;  
        }  
  
```  
and the output is 6 and not "\_\_LINE\_\_".

---

## Comment 2

> Username: ohhmm  
> Created at: 2021-08-18 08:38:28 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-900930594  

Visual Studio 2022 community preview version 17.0.0 Preview 3.  
Boost 1.76 installed to C:\Boost

---

## Comment 3

> Username: eldiener  
> Created at: 2021-08-18 13:25:56 UTC  
> Updated at: 2021-08-18 13:26:32 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901113965  

Can you try Visual Studio 2019 or Visual Studio 2019 Preview and see if you get the same results as with Visual Studio 2022 Preview ? I tried Visual Studio 2019 or Visual Studio 2019 Preview and I got the correct result. I really, really doubt that this is a Boost PP problem. Nothing in the BOOST_PP_STRINGIZE code has changed that would suggest a bug in Boost PP. If you can give me a command line with the compiler options you use it would be appreciated. I have downloaded Visual Studio 2022 Preview and will try it once I get it setup to use with Boost on my local machine.

---

## Comment 4

> Username: ohhmm  
> Created at: 2021-08-18 13:41:13 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901124961  

Here is where it used https://github.com/ohhmm/openmind/blob/703e42a40122f82ee7d8061fedd53b6740751e20/omnn/math/Valuable.h#L24  
If anything needed, surely I'll try to help.

---

## Comment 5

> Username: eldiener  
> Created at: 2021-08-18 14:43:12 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901174655  

Did you try my simple example above and see if the result matches mine ?

---

## Comment 6

> Username: ohhmm  
> Created at: 2021-08-18 15:30:02 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901211789  

It was ok with 2019

---

## Comment 7

> Username: eldiener  
> Created at: 2021-08-18 15:42:33 UTC  
> Updated at: 2021-08-18 15:48:07 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901221570  

Then it must be something with the new preview since it was also OK with the 2019 preview. If my simple example failed with 2022 preview and succeeds with VS2019, then you might want to report it as a bug to Microsoft. You can lift the code from the stringize.hpp header file in your report and massage it if you like, but it is really very simple. BOOST_PP_CONFIG_MSVC() in stringize.hpp is turned on for VC++ using the default preprocessor but is not set when the new preprocessor is being used. Either way, whether with the default preprocessor or the new preprocessor my tests showed that the correct result occurred with both VS2019 and VS2019 Preview. I would imagine that in the 2022 preview that the new preprocessor might now be the default but even in that case the code in stringize.hpp should work fine even if the code for the old, default preprocessor is being used, which I doubt is actually the case.

---

## Comment 8

> Username: eldiener  
> Created at: 2021-08-18 21:25:09 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901440556  

I was able to setup VS2022 Preview and tried my code above with both the default preprocessor and the new preprocessor and the result was correctly "6" and not "\_\_LINE\_\_".

---

## Comment 9

> Username: ohhmm  
> Created at: 2021-08-19 13:04:49 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-901898214  

I had this case: `__FILE__ ":" BOOST_PP_STRINGIZE(__LINE__) " "`

---

## Comment 10

> Username: eldiener  
> Created at: 2021-08-19 15:46:21 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-902024218  

I changed the program I used to test, called test_stringize.cpp, to:  
  
```  
# include <iostream>  
# include <boost/preprocessor/stringize.hpp>  
  
int main()   
    {  
    std::cout << '\n' << __FILE__ ":" BOOST_PP_STRINGIZE(__LINE__) " " << '\n';  
    return 0;  
    }  
```  
  
and in all cases the correct result of `test_stringize.cpp:6` was printed. This same correct result occured with msvc-14.2 msvc-14.2 preview and msvc-14.3 preview, whether I was using the default preprocessor or the new preprocessor. Would you please try the above program yourself ?

---

## Comment 11

> Username: ohhmm  
> Created at: 2021-08-19 18:28:25 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-902143652  

Sure, will do.

---

## Comment 12

> Username: ohhmm  
> Created at: 2021-08-20 11:51:33 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-902637401  

Reproduced in VS2022 Test Explorer window.   
This workaround works:  
![image](https://user-images.githubusercontent.com/190779/130229000-2b13479f-2bc1-45a9-8d14-38d49ddf1abc.jpeg)

---

## Comment 13

> Username: eldiener  
> Created at: 2021-08-20 11:55:09 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-902639015  

I can not read your image. Please copy as code if you want me to read it.

---

## Comment 14

> Username: eldiener  
> Created at: 2021-08-23 18:49:17 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-904022986  

I am assuming the problem is in your code and not in this library. Can I close this issue ?

---

## Comment 15

> Username: ohhmm  
> Created at: 2021-08-24 22:20:54 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-905015673  

![image](https://user-images.githubusercontent.com/190779/130697686-b4a7f79c-d80e-4dc9-a13f-6daef8c24031.png)

---

## Comment 16

> Username: ohhmm  
> Created at: 2021-08-24 22:26:05 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-905017891  

See the __LINE__ in the message. The workaround patch diff shown working well through third level of macro indirection is here https://github.com/ohhmm/openmind/commit/2a84c9db1a13776f63883d29b75b6fb4c737e633#

---

## Comment 17

> Username: eldiener  
> Created at: 2021-08-24 22:37:09 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-905022314  

I can not follow what you are trying to show me. You need to produce a simple code example which shows a problem with `BOOST_PP_STRINGIZE(__LINE__)`. That means not some image which it is impossible for me to follow.

---

## Comment 18

> Username: ohhmm  
> Created at: 2021-08-25 08:47:19 UTC  
> Updated at: 2021-08-25 08:48:24 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-905307282  

Hi Edward,  
  
It appears to be only reproduced inside the Visual Studio Test Console.   
So the simple test must be a boost test launched through the Visual Studio Tests Console.  
See the test output message on the screenshot, it contains `__LINE__`.   
Feel free to use the project Modulo test to reproduce.  
Simply adding one more macro indirection solves the problem.  
  
Thanks & Regards   
Serg

---

## Comment 19

> Username: andry81  
> Created at: 2021-08-25 11:56:33 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-905433180  

> It appears to be only reproduced inside the Visual Studio Test Console.  
  
Visual Studio Test Console is not a compiler. You have to reproduce the bug in a compiler.

---

## Comment 20

> Username: ohhmm  
> Created at: 2021-08-25 15:22:00 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/39#issuecomment-905604500  

Ok, I'll capture its params
