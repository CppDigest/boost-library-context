# #114 X3: Simplify lambda_visitor. [Closed]

> Username: mlang  
> Created at: 2015-06-02 20:11:49 UTC  
> Updated at: 2015-11-12 04:18:30 UTC  
> Closed at: 2015-06-04 02:01:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/114  

If I am not missing something obvious, there is actually no need for  
lambda_visitor to be as complicated as it is.

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-06-02 20:47:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108093945  

Are there tests for the multiple lambda case? The original version leverages the `using` keyword to bring in the overloads from the parent, so the outtermost instance had all overloads in the same visibility. I _think_ this verison is [ambigious](http://stackoverflow.com/questions/5368862/why-do-multiple-inherited-functions-with-same-name-but-different-signatures-not) with multiple lambdas, but I haven't tested this exact scenario (operators). It should be the same. I don't think its possible to expand the using declaration either here, I tried a couple of ways with clang 3.5. I don't think the C++ language syntax supports commas in this situation.

---

## Comment 2

> Username: djowel  
> Created_at: 2015-06-03 00:06:37 UTC  
> Updated_at: 2015-06-03 00:07:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108136331  

There's a plan to expand this to an overloads set. So consider this a work in progress. OTOH, perhaps it belongs somewhere else. How is it ambiguous? A test case would be good.

---

## Comment 3

> Username: vtnerd  
> Created_at: 2015-06-03 00:35:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108142567  

I think the patch here would create an ambiguous function call if multiple lambdas were provided, whereas the code on the develop branch won't. The `lambda_visitor` class would have an `operator()` defined in multiple of its children so anyone invoking `operator()` on `lambda_visitor` would have a compilation error. The [stackoverflow article](http://stackoverflow.com/questions/5368862/why-do-multiple-inherited-functions-with-same-name-but-different-signatures-not) linked above describes the same situation with named functions. The current version on the develop branch should not have this issue due to the recursive `using` statements.  
  
Also, I said I _think_ because clang 3.5 doesn't complain with `operator()`, but does complain if the function is given a name. gcc 4.8 complains in both situations. I wouldn't expect a special case for operators, but I haven't looked up the specification (stackoverflow provides a decent overview). Since @K-ballo filed this [lovely bug](https://llvm.org/bugs/show_bug.cgi?id=20222), I'm assuming its still an outstanding issue with Clang.

---

## Comment 4

> Username: djowel  
> Created_at: 2015-06-03 00:50:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108145030  

Ah I see. I should read more carefully. Happens when there's not enough caffeine :)

---

## Comment 5

> Username: K-ballo  
> Created_at: 2015-06-03 00:51:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108145066  

IIUC overloads are declarations with the same name in the same scope, while all the aggregated `operator()` in the proposed change obviously come from a different scope each. I believe the intended usage of the code to be ill-formed, but compilers disagree (in interesting ways). I'll consult a higher power and get back.

---

## Comment 6

> Username: mlang  
> Created_at: 2015-06-03 19:57:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108596555  

I am sorry for the noise.  While this initially looked like a very nice  
idea, I really should have tested more :-(.

---

## Comment 7

> Username: tomilov  
> Created_at: 2015-06-04 07:36:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/114#issuecomment-108766651  

I am sure [here](http://stackoverflow.com/a/30637053/1430927) is the answer.

---
