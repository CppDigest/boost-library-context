# #284 Allow mixed moves between related types. [Merged]

> Username: jzmaddock  
> Created at: 2020-12-31 19:54:14 UTC  
> Updated at: 2021-01-11 09:48:15 UTC  
> Merged at: 2021-01-11 09:48:14 UTC  
> Closed at: 2021-01-11 09:48:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284  

Fix for issue https://github.com/boostorg/multiprecision/issues/278.  
  
Allows more moves between related types, and direct operations on mixed types.  
  
@mglisse : This reduces your example to 2 allocations, likewise similar operations.

---

## Comment 1

> Username: mglisse  
> Created_at: 2020-12-31 21:05:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284#issuecomment-753155595  

Thanks, this looks great. Sorry it required so many changes in the comparison code...  
Do you expect `is_compatible_mixed_arithmetic_type` may eventually become a documented interface for people writing their own backend? Or maybe you prefer to give it some time, since it isn't clear yet if this should take 2 backends or 2 numbers, or other variants.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-01 16:35:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284#issuecomment-753340645  

>Do you expect is_compatible_mixed_arithmetic_type may eventually become a documented interface for people writing their own backend? Or maybe you prefer to give it some time, since it isn't clear yet if this should take 2 backends or 2 numbers, or other variants.  
  
Probably, but more experimentation may be required first.  
  
Right now I've just tried another sanity check - testing mixed arithmetic between `cpp_int` and `number<cpp_int_backend<N>>`  and there are gazillions of errors :(  The problem is that neither type is "better" than the other (ie they are both implicitly convertible to the other type since the precisions are the same) so the operator overloads are (rightly) all ambiguous.  I'm not sure how to fix this without yet another traits class which specifies the result type in case of ties like this, but it's not clear to me even what the default should be?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-01-02 16:21:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284#issuecomment-753492934  

>Right now I've just tried another sanity check - testing mixed arithmetic between cpp_int and number<cpp_int_backend<N>> and there are gazillions of errors :( The problem is that neither type is "better" than the other (ie they are both implicitly convertible to the other type since the precisions are the same) so the operator overloads are (rightly) all ambiguous. I'm not sure how to fix this without yet another traits class which specifies the result type in case of ties like this, but it's not clear to me even what the default should be?  
  
Ah, this is a non issue: if we've asked that two types be treated as equivalent then the notional result type of `a op b` is irrelevant and will be determined by the type we're assigning to anyway.  So we can just break the tie in the operator overloads either which way and everything should be fine.... testing now.

---

## Comment 4

> Username: mglisse  
> Created_at: 2021-01-02 16:38:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284#issuecomment-753495480  

> Ah, this is a non issue: if we've asked that two types be treated as equivalent then the notional result type of `a op b` is irrelevant and will be determined by the type we're assigning to anyway. So we can just break the tie in the operator overloads either which way and everything should be fine....  
  
Yes, as long as you use expression templates, you can use the type of the lhs. It is a bit strange for temporaries if the expression is complicated enough to require temporary variables, but not really a problem. And I'll propose a way to select a different type for temporaries later.  
With a template parameter to disable expression templates, then you do have to choose a type... Not sure if we should arbitrarily pick the first one, or somehow combine them (max of the maxbits, etc).

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-01-10 19:07:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284#issuecomment-757527033  

@mglisse just updated the docs which completes this I think.  The added examples/benchmarks show some nice improvements too.

---

## Comment 6

> Username: mglisse  
> Created_at: 2021-01-10 19:37:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/284#issuecomment-757531231  

Yes, I was just reading the new doc, it looks great, thanks a lot.  
I might have gone with `is_equivalent_number_backend<Backend1, Backend2>`, because it reduces the risk of instantiating `number<B>` before the partial specialization of `is_equivalent_number_type` is done (but I can't actually come up with a real issue this causes), and compatibility between the ET and non-ET versions of a type could be natural. I was also wondering about some trait like common_type, in case a mixed operation should return a 3rd type instead of just saying yes/no to compatibility, but that's a different feature. Those are just musings, I think the PR is fine as is (of course I didn't stress test it).  
Once this is in, the next step I would be interested in is a way for a backend to advertise a different, equivalent backend that should be used for temporaries inside number: for instance the evaluation of `a*b<c*d` uses 2 temporaries, and I'd like to automatically use a different type for those.

---
