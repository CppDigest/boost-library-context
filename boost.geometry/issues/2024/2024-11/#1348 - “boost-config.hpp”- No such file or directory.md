# #1348 - “boost/config.hpp”: No such file or directory [Closed]

> Username: yujream  
> Created at: 2024-11-25 08:37:32 UTC  
> Updated at: 2024-11-26 07:25:45 UTC  
> Closed at: 2024-11-25 18:12:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1348  

In the Develop branch, I included the `Include` folder into my project. When compiling, it is prompted that I can't find the `config.hpp` file. What should I do, thank you!

---

## Comment 1

> Username: vissarion  
> Created at: 2024-11-25 12:15:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1348#issuecomment-2497855761  

It is probably related to your local setup and how you include directories while compiling.

---

## Comment 2

> Username: yujream  
> Created at: 2024-11-25 15:38:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1348#issuecomment-2498357518  

> It is probably related to your local setup and how you include directories while compiling.  
  
I did not find the `config.hpp` file under the `include/boost/` folder. Do I need to put the `boost.geometry` module under the complete boost library?

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-11-25 18:12:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1348#issuecomment-2498718678  

It's here: https://github.com/boostorg/config/blob/5e98c6ddd4d58311b52e8f0fdde38c80c04f70ae/include/boost/config.hpp  
  
`Boost.Geometry` depends on several other `Boost` libraries. So if you want to work with it, you cannot just download Develop and go. You should either have Boost installed somewhere, and point to it, or first download the Boost Super project.  
  
The same holds for most Boost libraries, so it is not documented explicitly.  
  
https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview  
  
Closing as not a bug (you can ask questions in the `Discussion` tab)

---

## Comment 4

> Username: yujream  
> Created at: 2024-11-26 07:25:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1348#issuecomment-2499862923  

> It's here: https://github.com/boostorg/config/blob/5e98c6ddd4d58311b52e8f0fdde38c80c04f70ae/include/boost/config.hpp  
>   
> `Boost.Geometry` depends on several other `Boost` libraries. So if you want to work with it, you cannot just download Develop and go. You should either have Boost installed somewhere, and point to it, or first download the Boost Super project.  
>   
> The same holds for most Boost libraries, so it is not documented explicitly.  
>   
> https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview  
>   
> Closing as not a bug (you can ask questions in the `Discussion` tab)  
  
I will try it, thank you for answering!
