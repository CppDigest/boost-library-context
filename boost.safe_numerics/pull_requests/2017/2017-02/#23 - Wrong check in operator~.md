# #23 Wrong check in operator~ [Closed]

> Username: akrzemi1  
> Created at: 2017-02-20 22:46:09 UTC  
> Updated at: 2018-09-23 22:26:35 UTC  
> Closed at: 2017-04-09 17:43:11 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23  

But also, the error message seams to be confusing. operator~ is well defined for signed integers. Is it just your choice to exclude it from `safe<signed int>`?

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-02-20 23:25:07 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-281204898  

I recently considered this in some detail.  For unsigned types there's   
no problem.  
  
What does it mean to take the one's complement of an arithmetic type?    
What should it mean?  one some machines (which don't exist anymore) , it   
would mean to negate value).  Actually I'm pretty sure that if someone   
has done this in their program - it's a mistake.  I can't think any   
legitimate reason for wanting to do this so I'm trapping it.  The same   
occurs for binary operators ^, &.  I don't know if I've been consistent   
in the treatment of these things. Mix in integer promotion - (e.g.   
implicitly extending 0xffff to 0xffffffff for signed but maybe not for   
unsigned - I don't remember) and safe_literal - constants and things   
become even more weird.  
  
I've gone back and forth on the logical operators.  As time goes on, I'm   
coming around to the idea that application of logical operators to   
signed types is an arithmetic mistake and should be trapped in   
accordance with the overall purpose of the library - which is trap all   
cases where C/C++ conflicts with normal expectations for arithmetic.  
  
I'm sort of tentative about this though - It just requires more   
thought.  I'll try to go through this again and make sure that it's at   
least consistent.  
  
I'm very impressed with your attention to these details.  I've it   
impossible to make a library of this level of difficulty with out lot's   
of help from people such as yourself.  The same thing happened with the   
serialization library (it was probably worse though since I didn't know   
C++ all that well).  
  
Thanks again for your help.  
  
Robert Ramey  
  
  
  
On 2/20/17 2:46 PM, Andrzej Krzemieński wrote:  
>  
> But also, the error message seams to be confusing. operator~ is well   
> defined for signed integers. Is it just your choice to exclude it from   
> |safe<signed int>|?  
>  
> ------------------------------------------------------------------------  
>  
>  
>         You can view, comment on, or merge this pull request online at:  
>  
> https://github.com/robertramey/safe_numerics/pull/23  
>  
>  
>         Commit Summary  
>  
>   * Wrong check in operator~  
>  
>  
>         File Changes  
>  
>   * *M* include/safe_base.hpp  
>     <https://github.com/robertramey/safe_numerics/pull/23/files#diff-0>  
>     (2)  
>  
>  
>         Patch Links:  
>  
>   * https://github.com/robertramey/safe_numerics/pull/23.patch  
>   * https://github.com/robertramey/safe_numerics/pull/23.diff  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/pull/23>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3FStDY0wQdZ4a8zjocBhe0JB2L8aks5rehexgaJpZM4MGsF5>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2017-02-21 07:51:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-281269407  

My recommendation here would be to disable bit-wise operations, at least for now, and add to documentation that this is an open question. Likely, this removal will be permanent. In my experience, you either need arithmetical operations or bit-wise operations, but never do you need both. And from the user experience perspective, it is better to get something more in the newer release, than to be removed something.

---

## Comment 3

> Username: robertramey  
> Created_at: 2017-02-21 16:09:30 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-281390130  

There's more.  The same question appies to binary operations such as ^,   
^=, |, |=, etc. I think I'm going to settle on prohibiting all these and   
include the explanation of the rationale.  We'll see of someone raises   
the issue  
  
  
On 2/20/17 11:51 PM, Andrzej Krzemieński wrote:  
>  
> My recommendation here would be to disable bit-wise operations, at   
> least for now, and add to documentation that this is an open question.   
> Likely, this removal will be permanent. In my experience, you either   
> need arithmetical operations or bit-wise operations, but never do you   
> need both. And from the user experience perspective, it is better to   
> get something more in the newer release, than to be removed something.  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/pull/23#issuecomment-281269407>,   
> or mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3NqYbA18kU0X_m6uqV16U5OG_w31ks5repeBgaJpZM4MGsF5>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 4

> Username: akrzemi1  
> Created_at: 2017-02-21 16:19:11 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-281393235  

The consequence though is that `safe<int>` will not satisfy the concept of `Integer` as specified in the docs. But this should not be a problem `int` and `safe<int>` do not have to satisfy the same concept. `safe<T>` may require that `T` provides bit-wise operations, but need not expose them itself.

---

## Comment 5

> Username: nmusolino  
> Created_at: 2017-03-23 02:26:08 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-288598687  

I noticed the same problem, but please note that there is some surprising runtime behavior for `operator ~()` as detailed in Issue #50 .

---

## Comment 6

> Username: akrzemi1  
> Created_at: 2017-03-23 08:12:08 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-288645739  

There is a certain conflict of goals here. If `safe<int>` is to be a drop-in replacement for `int`, you will need to provide bitwise ops. But if the goal of `safe<int>` is to provide all operations on `int` but signal when they depart from mathematical expectations of Integers, then it should be noted that in mathematics there is no notion of storing integers in bits, and they can tell nothing about expectations on bit-wise operations. You would have to redefine your expectations on bit-wise ops.

---

## Comment 7

> Username: nmusolino  
> Created_at: 2017-03-23 11:47:01 UTC  
> Updated_at: 2017-03-23 11:48:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-288694097  

I understand what you're saying:  the notion of mathematical integers (which are addable, incrementable, etc) is different from the idea of binary-stored values in two's complement or one's complement.   
  
I actually agree with @robertramey that these two notions (mathematical integers and bit fields) are likely to be distinct use cases for primitive types like `unsigned int`.  the only intersection I can imagine is when developers use bitwise operations in place of certain arithmetic checks, e.g. computing `i & 0x07` to check if `i % 8 == 0`, but that is not very modern, and those tricks are best left to an optimizing compiler.  
  
So removing the bitwise operators seems sensible to me. It's definitely a judgment call, based on the overall goals of the library, but I think that dropping them is totally defend-able.

---

## Comment 8

> Username: robertramey  
> Created_at: 2017-04-08 22:21:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-292749681  

Just to keep you guys in the loop.  I'm still thinking on this.  As you all know, it seems simple.  But once one tries address all considerations like concepts, what users actually do, the library concept - drop in replacement, etc. one finds himself going back and forth.  As it currently stands, it handles all bit wise operations as I now want.  It does the same on signed and unsigned operands.  I have code to trap the operations on signed operands currently commented out.  But my thinking is that I will enable that code before I'm done.

---

## Comment 9

> Username: robertramey  
> Created_at: 2017-04-09 17:07:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-292798497  

OK - I looked into this a little bit more.  The current implementation is  
  
`    constexpr auto operator~() const { // complement  
        return ~Stored(0u) ^ *this;  
    }  
`  
 which should invert the bits of the variable and verify that the resultant value is a valid.

---

## Comment 10

> Username: robertramey  
> Created_at: 2017-04-09 17:43:11 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/23#issuecomment-292800511  

I added comments in code and documentation to clarify this.  
  
`    // after much consideration, I've permited the resulting value of a unary  
    // - to change the type.  The C++ standard does invoke integral promotions  
    // so it's changing the type as well.  
  
    /*  section 5.3.1 &8 of the C++ standard  
    The operand of the unary - operator shall have arithmetic or unscoped  
    enumeration type and the result is the negation of its operand. Integral  
    promotion is performed on integral or enumeration operands. The negative  
    of an unsigned quantity is computed by subtracting its value from 2n,  
    where n is the number of bits in the promoted operand. The type of the  
    result is the type of the promoted operand.  
    */  
    constexpr auto operator-() const { // unary minus  
        // if this is a unsigned type and the promotion policy is native  
        // the result will be unsigned. But then the operation will fail  
        // according to the requirements of arithmetic correctness.  
        // if this is an unsigned type and the promotion policy is automatic.  
        // the result will be signed.  
        return 0 - *this;  
    }  
    /*   section 5.3.1 &10 of the C++ standard  
    The operand of ~ shall have integral or unscoped enumeration type;   
    the result is the ones’ complement of its operand. Integral promotions   
    are performed. The type of the result is the type of the promoted operand.  
    */  
    constexpr auto operator~() const { // complement  
        return ~Stored(0u) ^ *this;  
    }  
`

---
