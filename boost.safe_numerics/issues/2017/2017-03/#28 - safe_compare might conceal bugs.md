# #28 - safe_compare might conceal bugs [Closed]

> Username: akrzemi1  
> Created at: 2017-03-03 08:54:04 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 22:08:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/28  

It is my understanding that this library can be used to determine where my usage of naked `int`s would result in a mathematically incorrect results. That is, If I use `safe<int>` in some part:  
  
```c++  
safe<int> compute(safe<int> a, safe<size_t> b);  
```  
and I test it, and it doesn't throw, then I am **guaranteed** that when I replace it with:  
  
```c++  
int compute(int a, size_t b);  
```  
And run the same test cases, I will not go into any UB or mathematically incorrect results.  
  
Is my understanding correct? Does this library give me such guarantee?  
  
I assume it does, and based on this assumption I report a failure. When comparing signed with unsigned `int`s:  
  
```c++  
safe<int> compute(safe<int> a, safe<size_t> b)  
{  
  if (a < b)  
    return safe<int>{1};  
  else  
    return safe<int>{0};  
}  
```  
  
The `safe<int>` does produce the correct answer all the time, but at the same time it conceals the information that there qould have been a problem with comparisons had I used naked `int`s. That is, the following works mathematically correct:  
  
```c++  
auto i = compute(-1, 100U);  
```  
No error or warning or even compile-time trap is reported. But if I now replace `safe<int>` back with `int`, I will get incorrect results.  
  
Maybe, you need a parametrized policy which would look like this:  
  
```c++  
using MyInt = safe<int, automatic, fix_or<throw_exception>>;  
```  
  
This 'parametrized' policy would mean "fix if you know how, otherwise fall back to the nested exception policy".  
  
---------------  
  
BTW, I think `safe_compare::not_equal` is never used.

---

## Comment 1

> Username: robertramey  
> Created at: 2017-03-03 16:31:34 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/28#issuecomment-284001536  

On 3/3/17 12:54 AM, Andrzej Krzemieński wrote:  
>  
> It is my understanding that this library can be used to determine   
> where my usage of naked |int|s would result in a mathematically   
> incorrect results. That is, If I use |safe<int>| in some part:  
>  
> safe<int>compute(safe<int> a, safe<size_t> b);  
>  
> and I test it, and it doesn't throw, then I am *guaranteed* that when   
> I replace it with:  
>  
> int  compute(int  a,size_t  b);  
>  
> And run the same test cases, I will not go into any UB or   
> mathematically incorrect results.  
>  
> Is my understanding correct? Does this library give me such guarantee?  
>  
Not in the example above.  The fact that it doesn't throw in one test   
doesn't guarantee that it won't  
throw in another test.  But consider this example from the documentation:  
  
using safe_int = safe<int, boost::numeric::native,   
boost::numeric::trap_exception>;  
using safe_size = safe<size, boost::numeric::native,   
boost::numeric::trap_exception>;  
  
safe_int compute(safe_int a, safe_size b)  
  
Now you compile your test or application.  If there is any possibility   
that an exception might be thrown at  
runtime, you get a compile time error.  It it compiles without error,   
you know with  
absolute certainty that it cannot return an arithmetically incorrect   
result.  There is no need  
for try/catch or any testing whatsoever.  There is no runtime overhead.  The  
program should run at the same speed as  
  
int compute( int a, size_t b);  
>  
> I assume it does, and based on this assumption I report a failure.   
> When comparing signed with unsigned |int|s:  
>  
> safe<int>compute(safe<int> a, safe<size_t> b)  
> {  
>    if  (a < b)  
>      return  safe<int>{1};  
>    else  
>      return  safe<int>{0};  
> }  
  
I would say:  
  
safe_int compute(safe_int a, safe_size b){  
   if  (a < b)  
     return  1; // invokes checked conversion to return type  
   else  
     return  0;  
}  
  
> The |safe<int>| does produce the correct answer all the time,  
>  
Right  
>  
> but at the same time it conceals the information that there qould have   
> been a problem with comparisons had I used naked |int|s.  
>  
Right - I see your point.  Maybe comparing objects with "trap" policy   
should fail at compile time in order to  
be consistent with the idea that we can later remove the "safe" types   
and still know that  
things are guaranteed correct.  I will consider this  
>  
> That is, the following works mathematically correct:  
>  
> auto  i = compute(-1,100U);  
>  
> No error or warning or even compile-time trap is reported. But if I   
> now replace |safe<int>| back with |int|, I will get incorrect results.  
>  
> Maybe, you need a parametrized policy which would look like this:  
>  
> using  MyInt = safe<int, automatic, fix_or<throw_exception>>;  
as I said above I think I want to be able to use:  
  
using MyInt = safe<int, native, trap_exception>  
  
and refine my usage of trap_exception.  
>  
> This 'parametrized' policy would mean "fix if you know how, otherwise   
> fall back to the nested exception policy".  
>  
Your usage would work.  But wouldn't recommend my self.  I don't like   
the idea that the meaning of the program  
varies depending on which policies we select.  Of course "automatic"   
type promotion violates this idea.  I can  
see the utility of it and people will want it, but once you've used it,   
you're changing the meaning of the C++ language.  
  
So I see the most useful policies as those which don't really alter the   
plain meaning of the code but trap or throw  
when the plain meaning of the code is incorrect.  So I like  
  
type promotion policies - native, cpp  
exception policies - throw, trap  
> ------------------------------------------------------------------------  
>  
> BTW, I think |safe_compare::not_equal| is never used.  
>  
LOL could well be.  When I'm writting one part of the library, I can't   
keep in mind the other parts.  Actually this is a good things as it   
forces me to avoid accidental coupling which makes the library harder to   
understand.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/robertramey/safe_numerics/issues/28>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3MIzdB5xrvuCUedVp2ebpxt6e7Zdks5rh9UugaJpZM4MSAjL>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-10 22:08:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/28#issuecomment-412219200  

I believe I've fixed all issues related to safe_compare.  safe_compare is actually misconceived. It changes the semantics of C++ there by hiding errors.  I have a couple in there still.  This is in tests where I need to verify that the test passed/failed.  Also I have some in the implementation of checked arithmetic.  They are necessary to implement checked arithmetic within the context of the C++ erroneous arithmetic.  So they are not visible to the user - just a rarely used implementation detail.

---

## Comment 3

> Username: robertramey  
> Created at: 2018-08-10 22:10:27 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/28#issuecomment-412219644  

I updated the document to this effect.
