# #2460 - Visual Studio CML instructions are out of date [Closed]

> Username: vinniefalco  
> Created at: 2022-06-18 15:06:18 UTC  
> Updated at: 2024-01-26 08:16:10 UTC  
> Closed at: 2024-01-26 08:16:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2460  

The README.md does not list the correct instructions for generating a Visual Studio project file. See: https://github.com/boostorg/json/blob/develop/README.md#visual-studio-solution

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-18 21:28:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2460#issuecomment-1159567701  

Doesn't CMake already come with a proper toolchain for this generator like any other generator? I've never seen any other boost or non-boost projects with specific instructions for Visual Studio.   
  
Is there still anything special about Visual Studio? I thought this was just regular CMake usage.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-21 23:21:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2460#issuecomment-1162455133  

No idea but I like having the instructions so I can copy and paste them. Since 2022 is not robust enough we can list 2019. And at some point we will want to modernize the CML for Beast but that can wait.

---

## Comment 3

> Username: ashtum  
> Created at: 2024-01-26 08:16:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2460#issuecomment-1911648470  

Addressed in https://github.com/boostorg/beast/pull/2787.
