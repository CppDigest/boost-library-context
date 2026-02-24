# #16 - Inconsistent triggering of anonymous transition after guarded transition [Closed]

> Username: sweatybridge  
> Created at: 2018-09-20 19:02:40 UTC  
> Updated at: 2024-02-13 09:13:24 UTC  
> Closed at: 2024-02-12 16:59:08 UTC  
> Url: https://github.com/boostorg/msm/issues/16  

Hi Christophe,  
  
I'm facing an unexpected behavior in the following transition table:  
```cpp  
    // guards  
    struct validate {  
        template<class Event, class FSM, class SourceState, class TargetState>  
        bool operator()(const Event& e, FSM&, SourceState&, TargetState&)  
        {  
            return e.value > 0;  
        }  
    };  
  
    // state machine properties  
    typedef boost::mpl::vector2<Running, Init> initial_state;  
  
    // Transition table for traceroute  
    struct transition_table : boost::mpl::vector<  
        //    Start      Event           Next      Action                     Guard  
        //  +----------+---------------+-----------+------------------------+--------------------+  
    bmf::Row< Running  , success       , bmf::none , log_action             , validate           >,  
        //  +---------+-------------+---------+---------------------------+----------------------+  
    bmf::Row< Init     , success       , Idle      , log_action             , bmf::none          >,  
    bmf::Row< Init     , failure       , Idle      , log_action             , bmf::none          >,  
    bmf::Row< Idle     , bmf::none     , Completed , log_action             , bmf::none          >  
        //  +---------+-------------+---------+---------------------------+----------------------+  
    > {};  
```  
  
The problem is when the `validate` guard returns false, the second orthogonal region correctly transitions to the `Idle` state but does not perform the anonymous transition from `Idle` to `Completed`. If I remove the first orthogonal region from the transition table, the anonymous transition is performed as expected.  
  
I think this behavior is inconsistent and would like to get your help in fixing it. The full program is posted here: https://gist.github.com/sweatybridge/b9b5d08046aee63e98d05653e7288fdf.  
  
Hope to hear from you soon.  
  
Warm Regards,  
Han

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-02-12 16:59:08 UTC  
> Url: https://github.com/boostorg/msm/issues/16#issuecomment-1939148684  

Test works for me. Added Unit Test to check it in future.

---

## Comment 2

> Username: sweatybridge  
> Created at: 2024-02-12 17:11:06 UTC  
> Updated at: 2024-02-12 17:11:46 UTC  
> Url: https://github.com/boostorg/msm/issues/16#issuecomment-1939172414  

Hello @henry-ch, did you check the stdout of the test? The test for `success0Bad` will run successfully but the end state is not correct.  
  
What it prints is  
```  
    // Actual:  
    // src: N3Bug4InitE dst: N3Bug4IdleE  
```  
  
But the correct state should be  
```  
    // Expected:  
    // src: N3Bug4InitE dst: N3Bug4IdleE  
    // src: N3Bug4IdleE dst: N3Bug9CompletedE  
```

---

## Comment 3

> Username: henry-ch  
> Created at: 2024-02-12 17:51:17 UTC  
> Url: https://github.com/boostorg/msm/issues/16#issuecomment-1939240221  

I did of course. Under Windows (more readable) I get:  
src: struct Bug::Init dst: struct Bug::Idle  
src: struct Bug::Idle dst: struct Bug::Completed

---

## Comment 4

> Username: sweatybridge  
> Created at: 2024-02-12 18:11:16 UTC  
> Url: https://github.com/boostorg/msm/issues/16#issuecomment-1939278170  

That's great. I was worried that the original issue wasn't clear.  
  
Thank you for providing more details of the fix, especially for those who missed the context.

---

## Comment 5

> Username: henry-ch  
> Created at: 2024-02-13 09:13:23 UTC  
> Url: https://github.com/boostorg/msm/issues/16#issuecomment-1940853196  

I do not know what actually fixed the issue, so I unfortunately have no details.
