# #1 Correct the use of propagate_on_container_move_assignment (add ::value) [Closed]

> Username: BenPope  
> Created at: 2014-04-23 16:16:04 UTC  
> Updated at: 2014-06-12 20:16:34 UTC  
> Closed at: 2014-05-14 02:22:58 UTC  
> Url: https://github.com/boostorg/container/pull/1  



---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-04-23 21:22:19 UTC  
> Url: https://github.com/boostorg/container/pull/1#issuecomment-41216511  

El 23/04/2014 18:16, BenPope wrote:  
  
>    git pull https://github.com/BenPope/container develop  
>   
> Or view, comment on, or merge it at:  
>   
> https://github.com/boostorg/container/pull/1  
> - Correct use propagate_on_container_move_assignment (add ::value)  
  
Thanks for the patch, merged and applied in commit:  
  
[develop 9b25c71] \* Fixed   
BOOST_CONTAINER_NOEXCEPT_IF(allocator_traits_type::propagate_on_container_move_assignment)   
missing ::value  
  
Thanks again

---

## Comment 2

> Username: BenPope  
> Created_at: 2014-05-14 02:22:58 UTC  
> Url: https://github.com/boostorg/container/pull/1#issuecomment-43035838  

Thank you

---
