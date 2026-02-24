# #76 - Adding a return value to "exception_caught" [Closed]

> Username: davidfrulm  
> Created at: 2024-07-23 16:09:37 UTC  
> Updated at: 2024-09-29 17:10:26 UTC  
> Closed at: 2024-09-29 17:10:26 UTC  
> Url: https://github.com/boostorg/msm/issues/76  

How feasible would be to add a return value to "exception_caught" so that we could decide if event is actually considered "handled"?  
  
template <class StateMachine, class Event>  
HandledEnum exception_caught(Event const& event, StateMachine& stateMachine, std::exception const& e)  
{  
    ...  
}  
  
That would allow clients to custom define how to treat exceptions for certain events.

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-07-25 07:04:48 UTC  
> Url: https://github.com/boostorg/msm/issues/76#issuecomment-2249606303  

It is a good idea and it would have been very feasible if I had thought about that in the first place. So now, I cannot change it without breaking existing code (C++ functions cannot be overloaded only on return type) so all I can think of is adding a new version:  
template <class StateMachine, class Event>  
HandledEnum exception_caught_return(Event const& event, StateMachine& stateMachine, std::exception const& e)  
{  
...  
}  
and then you could add a config flag in your concrete instance with typedef or using:  
typedef int use_exception_caught_with_return;  
Not very beautiful though. You also would have to use it for all events because you cannot partially specialize a function.  
Would that work for you?

---

## Comment 2

> Username: tleichtle  
> Created at: 2024-09-18 11:38:12 UTC  
> Url: https://github.com/boostorg/msm/issues/76#issuecomment-2358234847  

I think [this commit](https://github.com/boostorg/msm/commit/a78f50fa2e2107d221c8712015cdafd7b9d105e7) should have fixed the issue. Compared to the suggestion, it is not configurable, but it fixes the endless loop. I'm not aware of scenarios that would require the return value to be customizable.

---

## Comment 3

> Username: henry-ch  
> Created at: 2024-09-29 17:10:26 UTC  
> Url: https://github.com/boostorg/msm/issues/76#issuecomment-2381430071  

I thought it was a different request. Then I will close this issue.
