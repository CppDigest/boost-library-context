# #170 Documentation for special functions of tensors added [Open]

> Username: Neel-Shah-29  
> Created at: 2022-02-15 19:03:15 UTC  
> Updated at: 2022-03-05 06:10:39 UTC  
> Url: https://github.com/boostorg/ublas/pull/170  

I have added the documentation of special functions for tensors as instructed in proposal https://github.com/boostorg/ublas/discussions/167#discussioncomment-2166688

---

## Comment 1

> Username: amitsingh19975  
> Created_at: 2022-02-24 12:26:47 UTC  
> Updated_at: 2022-02-24 12:31:19 UTC  
> Url: https://github.com/boostorg/ublas/pull/170#issuecomment-1049809013  

You just added the file `special_functions.adoc`, but there is no link to the file. Please, add the link in the index to access it. Otherwise, users will not know about it. It's like you created a function, and you never used it.  
  
Furthermore, I would expect you to take some time and define each function properly without using a table. Each function to have its own section, where you define it, give the prototype, and examples using code blocks. Keep in mind to provide tensor specific behaviour, such as if a method is valid for static tensor or not, is there a tensor type-specific behaviour, etc.  
  
Also, think about readability than concise or cryptic documentation. Put yourself in the shoes of a newbie, who doesn't know about the library that well, and how will (s)he react to the documentation, will it help them to understand it.

---

## Comment 2

> Username: Neel-Shah-29  
> Created_at: 2022-02-24 16:02:36 UTC  
> Url: https://github.com/boostorg/ublas/pull/170#issuecomment-1050008428  

Hello, So for the first requirement i need to add the link of the files where all the special functions are used right?  
Also i will be giving explaination prototype and examples of each function using code blocks in some time, thanks for the suggestion.

---

## Comment 3

> Username: amitsingh19975  
> Created_at: 2022-02-24 16:06:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/170#issuecomment-1050011802  

> Hello, So for the first requirement i need to add the link of the files where all the special functions are used right?  
  
Read the existing document to see how to link the file or take help from @shikharvashistha.

---

## Comment 4

> Username: amitsingh19975  
> Created_at: 2022-02-25 11:57:12 UTC  
> Url: https://github.com/boostorg/ublas/pull/170#issuecomment-1050790645  

I'm sharing a screenshot of what I meant. Don't let your ideas be clouded by what I'm sharing, and add something you think would be much better than what I'm suggesting. My top priority for documentation is "readability" and "the user does not have to read the codebase to figure out how's the library works."  
  
<img width="740" alt="Screenshot 2022-02-25 at 5 17 43 PM" src="https://user-images.githubusercontent.com/29508454/155711300-acfc8133-74ca-49aa-b739-9501ad876e1a.png">

---

## Comment 5

> Username: Neel-Shah-29  
> Created_at: 2022-03-04 21:05:55 UTC  
> Updated_at: 2022-03-04 21:06:15 UTC  
> Url: https://github.com/boostorg/ublas/pull/170#issuecomment-1059530142  

Hello I have prepared the documentation for the special functions, I just wanted to ask that the code examples that needs to be added for each function should be taken from the codebase only right ?  i.e from [functions.hpp](https://github.com/boostorg/ublas/blob/master/include/boost/numeric/ublas/tensor/functions.hpp) where they are implemented

---

## Comment 6

> Username: amitsingh19975  
> Created_at: 2022-03-05 06:10:39 UTC  
> Url: https://github.com/boostorg/ublas/pull/170#issuecomment-1059703224  

> Hello I have prepared the documentation for the special functions, I just wanted to ask that the code examples that needs to be added for each function should be taken from the codebase only right ? i.e from [functions.hpp](https://github.com/boostorg/ublas/blob/master/include/boost/numeric/ublas/tensor/functions.hpp) where they are implemented  
  
Yes, you can also refer to the examples folder.

---
