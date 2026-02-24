# #58 - is_callable needs a more complete description [Closed]

> Username: viboes  
> Created at: 2015-10-04 02:29:50 UTC  
> Updated at: 2016-03-01 00:01:56 UTC  
> Closed at: 2015-12-17 20:30:55 UTC  
> Url: https://github.com/boostorg/hof/issues/58  

Is this compatible with INVOKE from the standard?

---

## Comment 1

> Username: K-ballo  
> Created at: 2015-10-04 14:26:39 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145354118  

`is_callable` cannot not be `INVOKE` based, as the term _callable_ defines things that can be used with `INVOKE`. Otherwise it would just be `is_function_object`.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-04 15:20:13 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145358108  

I'm confused. Your wording says "The expression INVOKE(declval<Fn>(), declval<ArgTypes>()..., R) is well formed when treated as an unevaluated operand. "

---

## Comment 3

> Username: K-ballo  
> Created at: 2015-10-04 15:29:32 UTC  
> Updated at: 2015-10-04 15:30:31 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145358619  

Perhaps my double negative threw you off?  
  
_callable_: `INVOKE(f, args...)`  
_function-object_: `f(args...)`  
  
If is not `INVOKE` based then is simply not `is_callable`.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-04 15:31:47 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145358714  

Right :)

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-05 06:38:17 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145443428  

It works for function objects and functions, since those are callable(ie you can use the call operator on them). It does not work for pointer to member functions or data, since they require some magical `INVOKE` operator to be called. I could add an `is_invokable` to the library, but for now its not necessary.  I will just add more precise documentation of `is_callable` semantics.

---

## Comment 6

> Username: K-ballo  
> Created at: 2015-10-05 06:45:35 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145444138  

Please don't try to redefine the standard term _callable_.

---

## Comment 7

> Username: pfultz2  
> Created at: 2015-10-05 07:48:27 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145452497  

So then what should a trait that checks for the call operator be called? In general, it is the name of the operator plus `-able`. Like the increment operator(`++`), would be called `is_incrementable`. So it seems appropriate that the trait for the call operator would be called `is_callable`.

---

## Comment 8

> Username: K-ballo  
> Created at: 2015-10-05 14:39:13 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145549376  

I don't have a satisfactory answer to that question. That's the **function call** operator and those are _function-objects_, so maybe `is_functionable`?  
  
Either way it's a disservice to your users. The standard library decided to support going forward calls regardless of syntax, what it defines as _callables_, and not just _function-objects_.

---

## Comment 9

> Username: pfultz2  
> Created at: 2015-10-05 16:20:27 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145589432  

> That's the function call operator and those are function-objects  
  
Its not just function objects. The call operator also applies to functions and function pointers, which `fit::is_callable` supports as well.  
  
>  so maybe is_functionable  
  
That doesn't work at all. "is functionable" means "does it work"(or "si sirve" in spanish).   
  
> The standard library decided to support going forward calls regardless of syntax, what it defines as callables, and not just function-objects.  
  
Then they should've update the language to make them actually callable, instead of redefining callable to mean invokable because now there is no simple way to refer to the call operator.  
  
Perhaps, I should call it `is_invokable` instead. Since callable means using the invoke operator(or `std::invoke`) then invokable can mean using the call operator. Its complementary, but completely confusing. I don't really like it.

---

## Comment 10

> Username: K-ballo  
> Created at: 2015-10-05 16:32:48 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145592343  

> That's the function call operator and those are function-objects  
>   
> Its not just function objects. The call operator also applies to functions and function pointers, which fit::is_callable supports as well.  
  
Function pointers (and pointers in general) are objects too.  
  
> The standard library decided to support going forward calls regardless of syntax, what it defines as callables, and not just function-objects.  
>   
> Then they should've update the language to make them actually callable, instead of redefining callable to mean invokable because now there is no simple way to refer to the call operator.  
  
You mean there's no simple way to refer to the **function call** operator. I would call that a success, the point is that _callables_ can be called, while _function-objects_ can have the function call operator applied to it.

---

## Comment 11

> Username: pfultz2  
> Created at: 2015-10-05 17:04:49 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145599985  

> Function pointers (and pointers in general) are objects too.  
  
Thats true, they just are not inheritable.  
  
> I would call that a success, the point is that callables can be called  
  
They cannot be called. They must be invoked, and thats the point.

---

## Comment 12

> Username: K-ballo  
> Created at: 2015-10-05 17:15:59 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145602678  

> I would call that a success, the point is that callables can be called  
>   
> They cannot be called. They must be invoked, and thats the point.  
  
You are entangling "call" to a function call syntax that applies a function call operator to a function-object. That's not a "call", that's a "function call".

---

## Comment 13

> Username: pfultz2  
> Created at: 2015-10-05 17:27:49 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145605457  

> You are entangling "call" to a function call syntax  
  
Of course, there is no other call syntax in C++.

---

## Comment 14

> Username: viboes  
> Created at: 2015-10-05 17:35:00 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145607369  

If your traits doesn't covers all the cases a function can be called `is_callable` is not the correct name.  
  
I believe that a functional library as yours can not ignore the INVOKE protocol used in the C++ standard. This will reduce the user expectation.

---

## Comment 15

> Username: K-ballo  
> Created at: 2015-10-05 17:53:06 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145613323  

> You are entangling "call" to a function call syntax  
>   
> Of course, there is no other call syntax in C++.  
  
You seem to be talking from the core language point of view, which only has the notion of a "function call". The standard library has a notion of "call", which is a different thing.  
  
You are choosing to give a well established standard terminology a different meaning. You are further choosing to offer inferior functionality than what it would be expected from a library on par with the standard library. This is the kind of feedback you will be receiving during a formal review.

---

## Comment 16

> Username: pfultz2  
> Created at: 2015-10-05 18:09:44 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145618923  

> I believe that a functional library as yours can not ignore the INVOKE protocol used in the C++ standard. This will reduce the user expectation.  
  
Supporting the INVOKE protocol would definitely increase compile time throughout the library. I don't see the benefit of adding that burden to everyone else for something that is sometimes never used. C++ is a language where you don't pay for what you don't use. Its easy enough to use `std::mem_fn` when using member pointers.

---

## Comment 17

> Username: K-ballo  
> Created at: 2015-10-05 18:21:31 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145621831  

You could just as well take function pointers only, it's just as reasonable a decision (no irony here). It would be surprising for a user, given that the standard library has generalized that to _function-objects_ for decades, and is now going for _callables_. But please please don't call that a _function-object_ if all you can take is a function pointer, that's just evil.  
  
If compile time efficiency is going to be the rationale for this design choice during the review, please prepare some concrete measurements to back it up, so we can have some technical discussion.

---

## Comment 18

> Username: pfultz2  
> Created at: 2015-10-05 18:21:52 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145621936  

> You are choosing to give a well established standard terminology a different meaning.   
  
That is not my intention, but because the standard has chosen to define callable to mean invokable, it leads to a difficult conundrum in finding terminology for something simple and straightforward as the call operator.

---

## Comment 19

> Username: K-ballo  
> Created at: 2015-10-05 18:25:18 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145622746  

> You are choosing to give a well established standard terminology a different meaning.  
>   
> That is not my intention, but because the standard has chosen to define callable to mean invokable, it leads to a difficult conundrum in finding terminology for something simple and straightforward as the call operator.  
  
By your earlier rationale of adding an `-able` suffix to the name of the operator, the correct name would be `is_function_callable`.

---

## Comment 20

> Username: pfultz2  
> Created at: 2015-10-05 18:35:28 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-145625213  

> By your earlier rationale of adding an -able suffix to the name of the operator, the correct name would be is_function_callable.  
  
That would work.

---

## Comment 21

> Username: pfultz2  
> Created at: 2015-12-17 20:30:55 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-165574187  

Well `is_callable` supports the generalized `INVOKE` operator, so I am closing this.

---

## Comment 22

> Username: viboes  
> Created at: 2016-02-29 23:42:50 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-190452536  

With the same INVOKE semantics than the standard? If yes, it is not worth describing it in the documentation, it is better to link to a paper.

---

## Comment 23

> Username: pfultz2  
> Created at: 2016-03-01 00:01:56 UTC  
> Url: https://github.com/boostorg/hof/issues/58#issuecomment-190456573  

Yes, thats described [here](http://pfultz2.github.io/Fit/doc/html/concepts/index.html#callable). However, `is_callable` doesn't require the `const T` as described. The is related #100 where there needs to be a description for `Callable` as well as `ConstCallable`.
