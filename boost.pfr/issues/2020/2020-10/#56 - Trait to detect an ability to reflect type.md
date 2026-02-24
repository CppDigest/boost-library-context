# #56 - Trait to detect an ability to reflect type [Open]

> Username: apolukhin  
> Created at: 2020-10-16 10:14:11 UTC  
> Updated at: 2023-11-23 10:01:57 UTC  
> Url: https://github.com/boostorg/pfr/issues/56  



---

## Comment 1

> Username: akrzemi1  
> Created at: 2020-10-19 20:23:50 UTC  
> Url: https://github.com/boostorg/pfr/issues/56#issuecomment-712420905  

Most probably, it is impossible to define a trait that will filter out aggregates with base classes. However, I would still recommend to define a concept (or type requirements, as they do in the Stndard Library) called `SimpleAggregate` or similar, and check as many things as you can (even if it is only to call `is_aggregate`). a concept has a value eve if it cannot statically check all its requirements.  
Then, in the documentation, in the reference section, for every function, specify that this template requires that the argument satisfies the requirements of `SimpleAggregate`.    
  
Then, if error message also mention name `SimpleAggregate`, it will be easier for programmers that get a compiler template error to figure out what they did wrong.

---

## Comment 2

> Username: schaumb  
> Created at: 2023-11-23 10:01:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/56#issuecomment-1824106120  

| it is impossible to define a trait that will filter out aggregates with base classes.   
  
It is not true. Use ubiq which can be converted only for the expected type base class (at size check, see below link).  
  
---  
  
There is a chance for a working trait to detect reflectable classes.  
- Use the loophole strategy to get the "aggregate constructor" types (+ [base classes](https://github.com/vizzuhq/vizzu-lib/blob/main/src/base/refl/auto_struct.h#L29) if aggregate).  
- Create a prf-defined tuple type from these types,  
- Check that pfr-defined tuple has the same layout.  
- Check that it has base class.  
  
=>  
  
This will filter out any non-same layout types (bitfields, packed structs, base classes)  
  
Outcomes:   
- The code can be split into types that contain "base class" or not. When contains a base class as a fallback can be used loophole strategy for the reflection (non constexpr way).
