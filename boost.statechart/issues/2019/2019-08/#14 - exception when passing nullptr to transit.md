# #14 - exception when passing nullptr to transit [Open]

> Username: PointClouder  
> Created at: 2019-08-27 08:38:22 UTC  
> Updated at: 2019-08-27 08:38:22 UTC  
> Url: https://github.com/boostorg/statechart/issues/14  

in my opinion a hand-tailored transition like  
  
`state::transit< Dest, Context, MyEvent >( nullptr, e );`  
  
where i pass an event without an action should be valid. however, transition function gets called in `transition_function::operator()` causing an exception derefencing a nullptr.   
  
usecase:  
access `e` in destintation state using `triggering_event()`
