# #39 - `-Wzero-as-null-pointer-constant` generates warnings in SFINAE [Closed]

> Username: cmazakas  
> Created at: 2021-11-18 18:39:40 UTC  
> Updated at: 2021-11-19 02:32:07 UTC  
> Closed at: 2021-11-19 02:32:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/39  

Users have noted that certain warnings will trigger for the SFINAE used in the main `implementation.hpp` file as noted here https://github.com/boostorg/unordered/pull/8  
  
Due to the busy lives of contributors, this issue can be directly tackled by a separate PR which refactors the SFINAE into the return type thus fixing the error and also updating the code to be more in line with current style recommendations.
