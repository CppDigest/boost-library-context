# #16 - Document division by 0 [Closed]

> Username: akrzemi1  
> Created at: 2017-01-12 15:53:40 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2018-08-10 22:41:37 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/16  

I think the docs should say somewhere how the division by zero is handled. This is a different kind of problem than exceeding the range, so it is not immediately obvious what the library will do. (I can see from the implementation that it checks for this also.)

---

## Comment 1

> Username: akrzemi1  
> Created at: 2017-01-20 08:43:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/16#issuecomment-274011450  

Or, to more clearly express the concerns. In the introductory section, to the question "why use safe<int> if a programmer can check the precondition manually" the documentation gives rationale: "because expressing the precondition is difficult, or next to impossible". Also, the library doesn't state this explicitly, but it can be seen as "mathematical integer, that throws upon resource exhaustion" (resource being a single int).  
  
Division by zero does not fit into any of the above. To check for it is easy. It cannot be seen as exhausting resources. Passing zero to division is a programmer error. Just throwing upon it is like saying "go ahead and pass us zero, we will handle it". It is like using `vector::at()` instead of `vector::operator[]`; or using garbage collector instead of RAII. It is like artificially widening the contract, and might make the detection of bugs harder.

---

## Comment 2

> Username: robertramey  
> Created at: 2017-01-20 16:22:49 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/16#issuecomment-274113786  

On 1/20/17 12:43 AM, Andrzej Krzemieński wrote:  
>  
> Or, to more clearly express the concerns. In the introductory section,   
> to the question "why use safe if a programmer can check the   
> precondition manually"  
>  
I don't see this sentence in my current version of the docs  
>  
> the documentation gives rationale: "because expressing the   
> precondition is difficult, or next to impossible".  
>  
nor do I see this one  
>  
> Also, the library doesn't state this explicitly, but it can be seen as   
> "mathematical integer, that throws upon resource exhaustion" (resource   
> being a single int).  
>  
I wouldn't look at it in this manner.  
>  
> Division by zero does not fit into any of the above. To check for it   
> is easy.  
>  
I disagree here.  consider  
  
int f(int a, int b){  
   return (a + b) / ( a - 2 * b);  
}  
  
The only way to address this by hand would be to insert another   
statement and error handling before the return.  This is exactly the   
requirement that we hope to avoid.  If trapping divide by zero fall   
doesn't fall within the intended purpose of the library, I would have to   
state it's purpose better.  I'll consider this. The purpose would be to   
produce programs in a convenient natural manner which trap all instances   
which would fail to return a valid arithmetical result.  
  
> It cannot be seen as exhausting resources. Passing zero to division is   
> a programmer error.  
>  
right - the problem is seeing it.  
>  
> Just throwing upon it is like saying "go ahead and pass us zero, we   
> will handle it".  
>  
It's more like saying, the programmer made a mistake here.  try to   
handle it if you want.  
>  
> It is like using |vector::at()| instead of |vector::operator[]|; or   
> using garbage collector instead of RAII. It is like artificially   
> widening the contract, and might make the detection of bugs harder.  
>  
I agree that your examples exemplify bad practices.   But I make a   
distinction here.  The current situation is that some bugs aren't   
detected (e.g. overflow), and others just crash the program on the spot   
(divide by zero).  This library provides a mechanism for  
1. detecting all these bugs  
2. and provides the option of handling them at run time.  
  
I think your criticism applies to 2. and the concern that users of this   
library would try to recover from fundamental errors in the program   
design.  Personally I wouldn't encourage this.  But I do see some cases   
where it might useful.  A program consisting of a collection of   
asynchronous tasks like an automobile could trap the problem, add a log   
entry, throw and reinitialize the task.  Not a ideal or permanent fix,   
but maybe better than just crashing the entire system.  Basically, I'm   
focused on detecting these problems (which are currently mostly not even   
detected).  I'm leaving the responsibility of deciding what to do when a   
problem is detected up to the user - which is the only thing I can do.    
This is the motivation behind error policy design.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/16#issuecomment-274011450>,   
> or mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3I7Wxv1hJlagZM1ahxkggJWmOo-Pks5rUHO0gaJpZM4Lh6Li>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 3

> Username: akrzemi1  
> Created at: 2017-01-20 22:49:46 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/16#issuecomment-274200936  

One thing we seem to agree upon  is that checking the division by zero is different from any other checks the library does. While all other checks check for places where the C++ int departs from the "algebraic" integer, the division-by-zero-check checks for the bugs within algebraic integer. Division by zero is an "algebraic error". Or to illustrate it differently, if I replaced my usage of `int` with `BigInteger` (which does memory allocation and can store arbitrarily big numbers), all the problems that safe<int> is trying to solve will be gone, except for division-by-zero.

---

## Comment 4

> Username: robertramey  
> Created at: 2018-08-10 22:41:34 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/16#issuecomment-412224804  

the exception policy and handling has been extensively changed to make it more correct and functional.  The documentation has been updated accordingly.  One of the tutorial examples addresses this very example.  So I think I've addressed this.  Thanks for looking into this.  Goading me to think about this myself made me really think about it to the point that I realized that original design was out o sync with reality.
