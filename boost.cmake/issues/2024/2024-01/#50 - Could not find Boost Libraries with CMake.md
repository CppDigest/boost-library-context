# #50 - Could not find Boost Libraries with CMake [Closed]

> Username: Proutsy  
> Created at: 2024-01-11 20:14:24 UTC  
> Updated at: 2024-01-12 13:37:38 UTC  
> Closed at: 2024-01-12 13:37:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/50  

Hi all,   
  
The Story:   
I am currently trying to build and run a GUI which requires the use of CMake and Boost. I had a struggle of my own trying to install Boost yesterday, but after installing a bit too many extensions for VS17 it was able to get past the bootstrap.bat step and download successfully.   
  
The Background:  
I am using Qt5.12.3, Boost 1.66.0, and CMake 3.15.4 alongside VS17. I have followed a guide to get to where I currently am. This guide has worked for two other people about 6 months ago, but me and someone else have had troubles trying to install it.   
  
The Error:   
When I open the project (exact same as the other people's project), CMake will give me this error while generating the cache:  
![image](https://github.com/boostorg/boost/assets/156349991/06996557-b9a7-4063-b7e0-c12aaf36e9e5)  
I honestly don;t know what to make of this; what it means, or how to fix it. I do know that the error is being caused in FindBoost.cmake's line 2044.   
The call to FindBoost occurs here:  
![image](https://github.com/boostorg/boost/assets/156349991/414e1a4e-264a-47f1-86e5-7a79bcca20a6)  
  
My Theories:  
- I think this is a simple bug that can be fixed by updating a environment variable or something similar?  
- Maybe I installed boost wrong somehow? I see all the libraries physically in its download though.   
- Perhaps the people who it worked with have a different type of hardware than I do?  
  
Any help or advice would be greatly appreciated!

---

## Comment 1

> Username: Proutsy  
> Created at: 2024-01-11 20:30:16 UTC  
> Url: https://github.com/boostorg/cmake/issues/50#issuecomment-1888054902  

To clarify: when opening the project, here is the output window for mine:  
![image](https://github.com/boostorg/boost/assets/156349991/7a92f247-46cb-4088-bcf6-ebe750e9d5dd)  
VS. the output window for another project member:  
![image](https://github.com/boostorg/boost/assets/156349991/8248249e-00d3-47c5-99e2-b0855c1ee333)  
It seems like an easy fix; how do I do it though? Any ideas?

---

## Comment 2

> Username: pdimov  
> Created at: 2024-01-12 00:46:45 UTC  
> Url: https://github.com/boostorg/cmake/issues/50#issuecomment-1888212908  

What guide are you following?

---

## Comment 3

> Username: Proutsy  
> Created at: 2024-01-12 13:37:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/50#issuecomment-1889229363  

> What guide are you following?  
  
Hi mate, I was using a custom guide made by a partner.   
I was able to figure the problem out though! Somehow, my boost zip file I was using didn't have all the data that the others' did. They sent me theirs and the program ran perfectly after extracting!
