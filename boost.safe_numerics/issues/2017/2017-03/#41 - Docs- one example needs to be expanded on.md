# #41 - Docs: one example needs to be expanded on [Closed]

> Username: akrzemi1  
> Created at: 2017-03-14 17:21:09 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-11 18:27:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/41  

The following is the discussion with Steve Watanabe. It looked like you were up to provide some example. I copy it here as a "todo" item.  
  
--------------------   
  
>  Unless I'm misunderstanding something, the use  of trap_exception will fail to compile at:  
>  `d *= velocity;`  
  
Hmmm - why do you think that?  
  
This is an example take from a real project - (for the tiny PIC  
processor).  I didn't make clear but LEMPARAMETER is a typedef  
for a 16 bit integer.  So this should never trap...  
  
Damn - I see you're correct here.  Now if I had written  
  
`d = velocity * velocity;`  
  
I would be golden!  
  
Nope, then there is the possibility that the d can't fit into a uint16  
so it could still be wrong.  I guess this illustrates the impossibility  
for normal people to actually write demonstrably correct code.  
  
I'm thinking the example should look more like:  
  
```  
// return value in steps  
/*  
Use the formula:  
    stopping dist = v **2 / a / 2  
*/  
uint16 get_stopping_distance(LEMPARAMETER velocity){  
    return velocity * velocity / lem.acceleration / 2;  
}  
```  
  
That should be provable correct !!!  
  
Damn - the correct return of uint16 can't be guaranteed at compile  
unless we do:  
  
```  
constexpr uint16 get_stopping_distance(LEMPARAMETER velocity){  
    return velocity * velocity / lem.acceleration / 2;  
}  
```  
  
and only call with constexpr argument.  
  
  
Ahhh - finally I see your point.  assignment using d as  
an accumuator loses the range calculated at compile time  
so subsequent operations can't be guaranteed to not  
overflow.  
  
I'll expand discussion of this example.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-11 18:27:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/41#issuecomment-412293303  

I've lost the context of this example.  I can summarize the the question in the following way:  
```  
using velocity_t = safe<std::uint16, trap_exception>;  
using acceleration_t = safe<std::uint16, trap_exception>;  
using distance_t = safe<std::uint16, trap_exception>;  
  
// will fail at compile time because it cannot be guaranteed to not overflow  
distance_t  get_stopping_distance(velocity_t v, acceleration_t a){  
    return v * v / a / 2;  
}  
  
// will pass at compile time if arguments are such that the result doesn't over flow  
constexpr distance_t get_stopping_distance(velocity_t v, acceleration_t a){  
    return v * v / a / 2;  
}  
  
using distance2_t = safe<std::uint32, trap_exception>;  
  
// will always pass at runtime regardless of arguments  
distance2_t get_stopping_distance(velocity_t v, acceleration_t a){  
    return v * v / a / 2;  
}  
// will always pass at compile time and runtime regardless of arguments  
constexpr distance2_t  get_stopping_distance(velocity_t v, acceleration_t a){  
    return v * v / a / 2;  
}  
  
```  
a) the above is my intention  
b) and represents the way things work now  
c) I'm not sure whether this is clear from the documents, examples, etc.  If it's not let me know.
