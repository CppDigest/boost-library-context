# #26 - Consider evaluation of expression on destruction; [Closed]

> Username: correaa  
> Created at: 2017-09-08 23:36:55 UTC  
> Updated at: 2019-06-04 00:30:54 UTC  
> Closed at: 2018-02-03 03:01:27 UTC  
> Url: https://github.com/boostorg/yap/issues/26  

Would it be possible to evaluate (by default or as an option) to evaluate expression upon destruction.   
  
For example, normally in expression template libraries, one has some sort of evaluate function,  
  
```  
terminal a = ...  
terminal b = ...  
terminal c = ...  
evaluate(a = b + c);  
```  
  
In principle, destruction on `;` could trigger the evaluation.   
```  
a = b + c;  
```  
I think it is possible to do because subexpression could be flagged not to evaluate upon their destruction.  
  
Another example from hello_world:  
  
```  
#include <boost/yap/expression.hpp>  
#include <iostream>  
int main (){  
//traditional:      
// evaluate(boost::yap::make_terminal(std::cout) << "Hello" << ',' << " world!\n");  
// automatic evaluation on destruction of a temporary  
    boost::yap::make_terminal(std::cout) << "Hello" << ',' << " world!\n";  
// or at least:      
//    boost::yap::make_auto_terminal(std::cout) << "Hello" << ',' << " world!\n";  
    return 0;  
}  
```  
This way the syntax will be more natural.   
What do you think?

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-03 03:01:27 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-362771942  

For now, I'm against this approach.  I think evaluations (and transforms) should be explicit.  While the "_auto" add to make_terminal() might technically count as explicit, I think many users' eyes would jump right past that.

---

## Comment 2

> Username: correaa  
> Created at: 2019-05-08 11:25:49 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-490449424  

Perhaps after our discussion on cppnow you might want to reconsider this. Perhaps I didn't word it well on the original issue post.

---

## Comment 3

> Username: tzlaine  
> Created at: 2019-05-09 08:47:53 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-490815662  

Yes, absolutely.  I have this on my TODO list now.  Thanks again for  
bringing it up.  
  
Zach  
  
On Wed, May 8, 2019, 5:25 AM Alfredo Correa <notifications@github.com>  
wrote:  
  
> Perhaps after our discussion on cppnow you might want to reconsider this.  
> Perhaps I didn't word it well on the original issue post.  
>  
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/yap/issues/26#issuecomment-490449424>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AAAUCAKNTYVGYNAOAKLCPNLPUK2D5ANCNFSM4D2HGIFA>  
> .  
>

---

## Comment 4

> Username: tzlaine  
> Created at: 2019-05-31 07:22:59 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-497605003  

Ok, the new example is ready; you're looking for the "Self-evaluating Expressions" exsample:  
  
https://www.boost.org/doc/libs/develop/doc/html/yap.html  
  
This should appear in the next Boost release.

---

## Comment 5

> Username: correaa  
> Created at: 2019-05-31 18:36:35 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-497817275  

Absolutely amazing, this is exact case (axpy) was the first thing I wanted to do the first time I used YAP.   
  
In principle in the hypothetical conceptual code  
```  
matrix m1{...}, m2{...}, m3{...};   
m1 = 3.0 * m2 + m3;  
```  
The only part that needs to be explicitly yapified (am I using it right?) is `m2` to trigger full control over the expression behavior (including the assignment). no?  
```  
m1 = 3.0 * expr(m2) + m3;  
```

---

## Comment 6

> Username: tzlaine  
> Created at: 2019-05-31 18:42:29 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-497819274  

Yes, that will do it.  If there is an optimization that applies to the left-hand side, though, you'd want to do:  
  
`expr(m1) = 3.0 * expr(m2) + m3;`  
  
In other words, you'd want to YAP-ifiy `m1` too, so that the entire assignment is captured as a transformable, optimizable expression.

---

## Comment 7

> Username: correaa  
> Created at: 2019-05-31 20:05:07 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-497843281  

well, that if somehow© `3.0*expr(m2) + m3` is not implicitly convertible  
(self evaluable) to the `matrix` type.  
  
On Fri, May 31, 2019 at 11:42 AM Zach Laine <notifications@github.com>  
wrote:  
  
> Yes, that will do it. If there is an optimization that applies to the  
> left-hand side, though, you'd want to do:  
>  
> expr(m1) = 3.0 * expr(m2) + m3;  
>  
> In other words, you'd want to YAP-ifiy m1 too, so that the entire  
> assignment is captured as a transformable, optimizable expression.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/yap/issues/26?email_source=notifications&email_token=AAXICU4F6Y4QI5Y3JAVN7T3PYFWRNA5CNFSM4D2HGIFKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODWWB5CQ#issuecomment-497819274>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAXICUZE2VOHSQWI56CIIZLPYFWRNANCNFSM4D2HGIFA>  
> .  
>

---

## Comment 8

> Username: correaa  
> Created at: 2019-06-04 00:01:07 UTC  
> Updated at: 2019-06-04 00:19:10 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-498468835  

On Fri, May 31, 2019 at 1:04 PM Alfredo Correa <alfredo.correa@gmail.com>  
wrote:  
  
> well, that if somehow© `3.0*expr(m2) + m3` is not implicitly convertible  
> (self evaluable) to the `matrix` type.  
>  
  
Can one make `matrix m1; m1 = 3.0*expr(m2) + m3` work within the library, or after a  
minimal extension.  
  
>  
> On Fri, May 31, 2019 at 11:42 AM Zach Laine <notifications@github.com>  
> wrote:  
>  
>> Yes, that will do it. If there is an optimization that applies to the  
>> left-hand side, though, you'd want to do:  
>>  
>> expr(m1) = 3.0 * expr(m2) + m3;  
>>  
>> In other words, you'd want to YAP-ifiy m1 too, so that the entire  
>> assignment is captured as a transformable, optimizable expression.  
>>  
>> —  
>> You are receiving this because you authored the thread.  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/yap/issues/26?email_source=notifications&email_token=AAXICU4F6Y4QI5Y3JAVN7T3PYFWRNA5CNFSM4D2HGIFKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGODWWB5CQ#issuecomment-497819274>,  
>> or mute the thread  
>> <https://github.com/notifications/unsubscribe-auth/AAXICUZE2VOHSQWI56CIIZLPYFWRNANCNFSM4D2HGIFA>  
>> .  
>>  
>

---

## Comment 9

> Username: tzlaine  
> Created at: 2019-06-04 00:10:54 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-498470726  

I don't really understand the question.  You just "made" `m1 = 3.0*expr(m2) + m3` within the question itself, didn't you?  I don't really get what you're asking.

---

## Comment 10

> Username: correaa  
> Created at: 2019-06-04 00:20:21 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-498472455  

Sorry, the word "work" after the code was missing.   
`matrix m1; m1 = 3.0*expr(m2) + m3`.   
Where `matrix` is a concrete matrix not an expression. Types in the expression somehow imply the need for an evaluation on the rhs.

---

## Comment 11

> Username: tzlaine  
> Created at: 2019-06-04 00:30:53 UTC  
> Url: https://github.com/boostorg/yap/issues/26#issuecomment-498474214  

Yes, that works.  That's covered in the example by the `operator auto() const` conversion operator on the primary expression template.  
  
One other way you could accomplish this, that I did not show in the example, would be to add a templated ctor to the matrix type:  
  
```  
struct matrix {  
    template <typename Expr>  
    matrix (Expr && expr) {  
        // Might be better expressed via SFINAE or via concept.  
        static_assert(boost::yap::is_expr<Expr>::value, "a YAP expression is required");  
        *this = optimize_and_evaluate(expr);  
    }  
    // ...  
};  
```
