# #6 - Does flyweight support float/double? [Closed]

> Username: akeyliu  
> Created at: 2020-11-15 03:15:43 UTC  
> Updated at: 2020-11-16 02:02:27 UTC  
> Closed at: 2020-11-16 02:02:27 UTC  
> Url: https://github.com/boostorg/flyweight/issues/6  

1. I'm a new guy to using boost/flyweight, but use many other boost libs, i.e. date time, program options, etc.  
2. I know that flyweight can be used only when the new template shoule satisfy the follow: For flyweight<T> to be instantiable, T must be Assignable, Equality Comparable and must interoperate with Boost.Hash  
3. For float/double type in C++, there should no equality comparable for them, and these types are common ones for many program, i.e. some fixed limit for parameters.   
So, If I want to using flyweight for float/double in my project. (1) If boost/flyweight is support using float/double yet? or (2) I need add some code, i.e. overwright == operator for float/double, or (3) Some other  suggest code to support float/double as flyweight type?  
Thanks for any of your response and suggestion.

---

## Comment 1

> Username: akeyliu  
> Created at: 2020-11-16 02:02:27 UTC  
> Url: https://github.com/boostorg/flyweight/issues/6#issuecomment-727689354  

I've check the point address for flyweight of float/double which is same when using flyweight.  
So,  Flyweight is support float/double.
