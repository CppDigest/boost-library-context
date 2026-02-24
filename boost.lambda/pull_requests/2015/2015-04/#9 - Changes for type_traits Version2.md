# #9 Changes for type_traits Version2 [Merged]

> Username: eldiener  
> Created at: 2015-04-23 17:09:23 UTC  
> Updated at: 2015-05-18 23:04:39 UTC  
> Merged at: 2015-05-18 23:04:39 UTC  
> Closed at: 2015-05-18 23:04:39 UTC  
> Url: https://github.com/boostorg/lambda/pull/9  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-04-23 18:02:02 UTC  
> Url: https://github.com/boostorg/lambda/pull/9#issuecomment-95672260  

I have a question about this change - is it not overkill to use mpl::and etc?  These were workarounds for broken compilers that couldn't handle use of && || ! etc in constant expressions (just like the old ice_or/ice_and were).  Is it not simpler and more readable to compose the expression directly using boolean operators?

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-04-23 20:12:54 UTC  
> Url: https://github.com/boostorg/lambda/pull/9#issuecomment-95706519  

You mean that &&, !!, and ! actually work with compile-time constant boolean expressions <g> ?  
  
You are right. I took the safest way I knew, which was to replace all of the ice functionality with their equivalent MPL functionality. I have been so brainwashed by how well MPL  works, once I understood it, that I honestly never even thought of using the logical operators directly.  
  
If you think it correct not to worry about the broken compilers anymore I can rework my changes. I made the same type of changes for the other libraries where I found the deprecated type_traits ice headers were being used.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-04-23 20:13:51 UTC  
> Url: https://github.com/boostorg/lambda/pull/9#issuecomment-95706708  

"You mean that &&, !!, and ! actually work with compile-time constant boolean expressions ?" This should be a smiley.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-04-24 12:24:24 UTC  
> Url: https://github.com/boostorg/lambda/pull/9#issuecomment-95917117  

> You are right. I took the safest way I knew, which was to replace all   
> of the ice functionality with their equivalent MPL functionality. I   
> have been so brainwashed by how well MPL works, once I understood it,   
> that I honestly never even thought of using the logical operators   
> directly.  
>   
> If you think it correct not to worry about the broken compilers   
> anymore I can rework my changes. I made the same type of changes for   
> the other libraries where I found the deprecated type_traits ice   
> headers were being used.  
>   
>  From memory, the workarounds were only really introduced for VC6: which   
> is definitely not supported any more.  Personally, I find code that uses   
> the operators directly is cleaner and easier to understand - it should   
> also result in many fewer template instantiations which is a good thing.  
  
Either way, many thanks for submitting PR's for these issues - I've been   
sort of holding off on these changes until the revised type_traits   
actually makes it into develop.

---

## Comment 5

> Username: eldiener  
> Created_at: 2015-04-27 07:01:58 UTC  
> Url: https://github.com/boostorg/lambda/pull/9#issuecomment-96524904  

I have changed this to use operators directly rather than mpl.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-04-27 07:26:39 UTC  
> Url: https://github.com/boostorg/lambda/pull/9#issuecomment-96529817  

> I have changed this to use operators directly rather than mpl.  
>   
> OK, thanks!

---
