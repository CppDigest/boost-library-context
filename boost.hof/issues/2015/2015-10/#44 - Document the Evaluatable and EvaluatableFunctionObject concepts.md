# #44 - Document the Evaluatable and EvaluatableFunctionObject concepts [Closed]

> Username: ldionne  
> Created at: 2015-10-04 01:57:48 UTC  
> Updated at: 2015-10-30 23:04:42 UTC  
> Closed at: 2015-10-30 23:04:42 UTC  
> Url: https://github.com/boostorg/hof/issues/44  

The `apply_eval` function documents that it requires `Evaluatable` arguments. I wasn't able to find that concept, what is it? Similarly, the `eval` function requires `EvaluatableFunctionObject`, which does not seem to be documented.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-25 00:47:52 UTC  
> Url: https://github.com/boostorg/hof/issues/44#issuecomment-150876650  

They really are the same. `Evaluatable` means you can call `eval(f)` and `f` has the requirements of `EvaluatableFunctionObject`. I added `EvaluatableFunctionObject` to the documentation. It is a bit unusual since it has an either-or type definition(ie it is either a `NullaryFunctionObject`, or it is an `UnaryFuntionObject` that accepts the `identity` function as a parameter)

---

## Comment 2

> Username: ldionne  
> Created at: 2015-10-25 01:03:44 UTC  
> Url: https://github.com/boostorg/hof/issues/44#issuecomment-150878049  

Cool for `EvaluatableFunctionObject`, but shouldn't `Evaluatable` be documented too somehow? I understand that it's a bit simplistic, but for completeness?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-25 01:09:45 UTC  
> Url: https://github.com/boostorg/hof/issues/44#issuecomment-150878386  

Well, I am thinking of replacing `Evaluatable` with `EvaluatableFunctionObject`. So there will only be one concept instead of two.
