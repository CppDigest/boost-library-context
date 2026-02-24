# #29 - Non standard return fails to compile [Open]

> Username: zofer1  
> Created at: 2025-10-28 15:17:50 UTC  
> Updated at: 2025-10-29 13:53:44 UTC  
> Url: https://github.com/boostorg/array/issues/29  

This statement:  
return BOOST_ASSERT_MSG( i < N, "out of range" ), elems[i];  
  
is a non-standard form of return.  
  
In case we compile optimized code that lacks assert function but still want something this code fails to compile.  
  
Separating this to a more standard form solves the issue:  
  
BOOST_ASSERT_MSG( i < N, "out of range" );  
return  elems[i];

---

## Comment 1

> Username: pdimov  
> Created at: 2025-10-28 15:40:15 UTC  
> Url: https://github.com/boostorg/array/issues/29#issuecomment-3457145777  

There's nothing non-standard about it.  
  
What's the actual error you're getting, and under what circumstances does it occur?

---

## Comment 2

> Username: zofer1  
> Created at: 2025-10-29 05:50:35 UTC  
> Url: https://github.com/boostorg/array/issues/29#issuecomment-3459954425  

Hi,  
The error we get is this:  
  
For below failure  
  
11:33:01 [INFO] [echo] .sb/tp_include/boost/array.hpp:109:20: error:  
expected primary-expression before 'if'  
  
11:33:01 [INFO] [echo]              return BOOST_ASSERT_MSG( i < N, "out of  
range" ), elems[i];  
11:33:01 [INFO] [echo]  
  
While this form of return with two arguments adheres to the C++ standard,  
the common use of return statement is with a single argument.  
  
I could not find a simple example here:  
https://en.cppreference.com/w/cpp/language/return.html  
  
It took me some time to understand how it works.  
  
If I understand the context here, the first expression that evaluates to  
true is used for return.  
  
As we wish to have here an assert with this message when using debug  
versions.  
  
Anyways, unless you are truly familiar C++ and try to make some sense of  
it, you will find it confusing.  
  
If we really want to have an assert message and avoid accessing array items  
beyond the array boundaries and potential segmen tviolation, there are more  
readable ways to do that.  
  
If this is really interesting I will look up the exact flags combination  
that puts us in this compilation failure, but this should be modified for  
clarity regardless.  
  
Cheers,  
Zohar  
  
On Tue, 28 Oct 2025 at 17:46, Peter Dimov ***@***.***> wrote:  
  
> *pdimov* left a comment (boostorg/array#29)  
> <https://github.com/boostorg/array/issues/29#issuecomment-3457145777>  
>  
> There's nothing non-standard about it.  
>  
> What's the actual error you're getting, and under what circumstances does  
> it occur?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/array/issues/29#issuecomment-3457145777>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABU4EA6FLP2ICJ2HSSLAEBL3Z6FXFAVCNFSM6AAAAACKOMCLTGVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZTINJXGE2DKNZXG4>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: pdimov  
> Created at: 2025-10-29 11:17:22 UTC  
> Url: https://github.com/boostorg/array/issues/29#issuecomment-3460993908  

> 11:33:01 [INFO] [echo] .sb/tp_include/boost/array.hpp:109:20: error:  
> expected primary-expression before 'if'  
  
This means that you're defining `BOOST_ASSERT_MSG` to something with `if` in it. This is not the definition in `boost/assert.hpp`; you must be getting it from somewhere else.  
  
> While this form of return with two arguments adheres to the C++ standard,  
> the common use of return statement is with a single argument.  
  
This is not a two argument `return`; it's a comma expression.  
  
https://en.cppreference.com/w/cpp/language/operator_other.html  
  
(See section "Built-in comma operator".)

---

## Comment 4

> Username: mclow  
> Created at: 2025-10-29 13:53:44 UTC  
> Url: https://github.com/boostorg/array/issues/29#issuecomment-3461657594  

Some versions of C++ require that constexpr functions consist of only a single statement.  
  
Rewriting the code as you suggest would mean that it was two statements, and could no longer be used in constant expressions.
