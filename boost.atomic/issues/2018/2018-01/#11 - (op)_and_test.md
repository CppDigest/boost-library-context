# #11 - (op)_and_test [Closed]

> Username: uecasm  
> Created at: 2018-01-24 06:04:14 UTC  
> Updated at: 2018-01-28 17:52:45 UTC  
> Closed at: 2018-01-28 17:52:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/11  

It seems like an unfortunate choice that these return true when the result is zero, rather than when it is non-zero.  It's the opposite of both the standard if-operator-bool logic and the bitwise test operations.  
  
Any chance this can be "fixed" for the next version, breaking change though that would be?

---

## Comment 1

> Username: Lastique  
> Created at: 2018-01-24 09:51:42 UTC  
> Url: https://github.com/boostorg/atomic/issues/11#issuecomment-360077690  

The main use case for these operations is when you want to test for zero, so instead of e.g.  
  
    if (a.fetch_sub(1) - 1 == 0)  
  
you would write  
  
    if (a.sub_and_test(1))  
  
I'm not sure if the opposite form is better:  
  
    if (!a.sub_and_test(1))  
  
I'm not opposed to changing it but it would be a breaking change, so there has to be a good motivation. I think we should ask for community opinion on this and probably pick a better name for such functions.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-01-28 17:52:45 UTC  
> Url: https://github.com/boostorg/atomic/issues/11#issuecomment-361081622  

Done in b24cea0af1b0d477f202d0539bdacfec543d0058.  
  
You can define `BOOST_ATOMIC_HIGHLIGHT_OP_AND_TEST` when compiling your code to highlight all uses of the changed functions so that it is easier to find and update the affected code.
