# #2678 - unreachable code in asio/buffer.hpp with optimizations [Closed]

> Username: Dragonexodus  
> Created at: 2023-05-02 06:12:43 UTC  
> Updated at: 2024-01-22 16:25:57 UTC  
> Closed at: 2024-01-22 16:25:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2678  

See References #1582   
  
@vm2mv wrote:  
  
> boost 1.81 and 1.82  
> Visual Studio 2022 17.4.3  
>   
>     Disabling inline function expansion (/Ob0) moves the warning from asio to beast:  
>   
> boost\beast\core\impl\buffers_cat.hpp(206): warning C4702: unreachable code   
>   
>         template<class I>  
>         reference operator()(I)  
>         {  
> ->          return *self.it_.template get<I::value>();  
>         }  
>   
  
I'm using boost-beast in combination with VCPKG.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-05-03 02:57:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1532401593  

Yeah this has been torturing me for a few years now...

---

## Comment 2

> Username: Dragonexodus  
> Created at: 2023-05-03 04:37:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1532445803  

:( Sorry to hear!  
Interestingly Version 1.80 and prior are working with VCPKG and static build.  
So the questions is.. what changed.  
Does this change: https://github.com/ImageEngine/cortex/pull/1217 had a an impact on the compiler behaviour?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-05-15 01:23:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1547065757  

@Dragonexodus can you check if changing the function in question to   
  
```cpp  
        template<std::size_t I>  
        reference operator()(mp11::mp_size_t<I>)  
        {  
            return *self.it_.template get<I>();  
        }  
```  
  
Fixes the warning?

---

## Comment 4

> Username: Dragonexodus  
> Created at: 2023-05-16 08:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1549225318  

Sorry, currently I can't test this change, because I only use beast directly over VCPKG (as static) without any other adjustment.  
  
Maybe @vm2mv could test the fix?

---

## Comment 5

> Username: IamVishalSharma  
> Created at: 2023-06-01 02:59:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1571254684  

Any update on this .. i am hitting the same issue with Version 1.82 and vc142/143 tool chain Visual Studio 2022 ( 17.6.2)

---

## Comment 6

> Username: hans-brgs  
> Created at: 2023-07-19 14:10:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1642163644  

I am experiencing the same issue as described in this thread.  
  
`buffers_cat.hpp(186): warning C4702: unreachable code`  
  
Has anyone else encountered this problem and found a solution or workaround? I'd appreciate any insights or suggestions.  
  
Best,

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-07-20 01:59:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2678#issuecomment-1642993095  

There's a workaround above. I just need confirmation that it solves the problem before merging.
