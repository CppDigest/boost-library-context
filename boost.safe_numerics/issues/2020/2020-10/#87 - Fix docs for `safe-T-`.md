# #87 - Fix docs for `safe<T>` [Closed]

> Username: akrzemi1  
> Created at: 2020-10-13 19:33:40 UTC  
> Updated at: 2020-10-17 04:10:35 UTC  
> Closed at: 2020-10-17 04:10:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/87  

The doc for `safe<T>` at https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/safe.html need some fixes:  
  
  1. Section "Model of" lists `Integer` which is a link to https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/numeric.html  
      It should be a link to https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/integer.html instead.  
  2. Type requirements `Integer<T>` in table "Template Parameters" link to Cppreference documentation for `std::is_integer`.   
      They should instead link to concept `Integer` at https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/integer.html  
  3.  Similarly, type requirement `PromotionPolicy<PP>` do not link to https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/promotion_policy.html  
  4.  Similarly, type requirements `Exception Policy<EP>` do not link to https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/exception_policy.html  
  5. Section "Associated types" mentions types: `PP` and `EP`. But they are not associated types: `safe<int>::EP` does not work.  
  6. There is no synopsis for the operations of `safe<T>`. Only a link to concept `SafeNumeric`, but this is not sufficient.   
      There is no way to figure out from the documentation under which conditions operations such as addition will signal error.   
      For instance, can unary operator minus report an error? How do I know it from the docs? Can the default construction of `safe<int>` throw an exception (this is creating a variable with indeterminate value). How do I know which policy member is called in that case?

---

## Comment 1

> Username: robertramey  
> Created at: 2020-10-14 12:59:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/87#issuecomment-708385426  

"Section "Associated types" mentions types: PP and EP. But they are not associated types: safe<int>::EP does not work."  
   
But safe<int, PP, EP> does. So there is an association.  hmmm but them PP and EP aren't types.  Perhaps the section title should be changed from "Associated Types" to "Associated Concepts".   I'm going to do this for now.  
  
Hmmm - I just did this but now I see it's in the Template Parameters section.  So I'm now just inclined to drop the whole section "Associated Types".  
  
"There is no synopsis for the operations of safe<T>. Only a link to concept SafeNumeric"  
  
Hmm - I think it SHOULD be sufficient.  I think the fact that the links to SafeNumeric and Integer concepts are wrong confuse the issue.  The intent is that every valid operation for Integer<T> and/or SafeNumeric<T> is valid for safe<T>.  
  
For example: "can unary operator minus report an error".  The procedure should be:  
  
Check the concept Integer<T> - (assume that the broken link is fixed)  one will find a list of valid operations.  the unary minus is not in there, so it can't be used.  BUT WAIT Integer<T> is a refinement of Numeric<T> and unary minus IS a valid expression for all types which implement the concept of Numeric<T>.  Since all types which implement Integer<T> must necessarily implement Numeric<T>, the unary minus IS supported.  
  
So I think there is no error here.  But, I concede that it's a little terse and perhaps some clarifying text should be added.  And fixing the links will help.  This might seem a little formal/abstract.  But it leaves open things like support of floating point and perhaps other types of numbers.    
  
"There is no way to figure out from the documentation under which conditions operations such as addition will signal error."  
  
Note that the Description of safe<T> has one important line:   
  
"A safe<T, PP , EP> can be used anywhere a type T can be used. Any expression which uses this type is guaranteed to return an arithmetically correct value or to trap in some way."  
  
So we know that when using safe<T, PP, EP> no error is left unaddressed.  To know whether the "trap" is implemented as exception, compile time trap, etc. one has to consult the concept for the EP parameter.  
  
"Can the default construction of safe<int> throw an exception (this is creating a variable with indeterminate value)"  
  
The documentation for ExceptionPolicy<EP> describes how one specifies how this situation should be handled.  The EP is actually a composite type with for members - one of which is to handle the question of initialized values.  To make things easier for the user, there are a number of pre-made "models".  The most likely used one (the default) would be "boost::numeric::loose_exception_policy which doesn't enforce initialization.  I made so that it would be easy to apply to already made code without have to do whole sale changes.  I would recommend a more strict policy for new code.  
  
I hope this clarifies things.  If nothing else, I'm sure it helps you appreciate how much more difficult this ends up being as opposed to how it starts out.  I'm very impressed you can read this and understand it at this level of detail without dosing off.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2020-10-14 13:18:22 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/87#issuecomment-708395950  

Regarding the issue with `PP` and `EP`, I think the problem stems from the fact that the [linked page]( https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/safe.html) describes a class template (`safe<>`) but it uses the method of description for concepts (`safe<>` is not a concept).  
  
If we look at the [SGI documentation](http://www.rrsd.com/software_development/stl/stl/for_each.html) for templates (rather than concepts), it uses section "Requirements on types" for this purpose. his would work for your case.   
  
BTW, thank you for this backup of SGI docs!

---

## Comment 3

> Username: akrzemi1  
> Created at: 2020-10-14 13:33:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/87#issuecomment-708404512  

I realize, I have reported a number of problems under this issue. I will separate them into smaller issues, so that each one can be discussed and tracked separately.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-10-17 04:10:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/87#issuecomment-710743933  

closing this - feel free to re-open if necessary.
