# #76 - Arithmetic on safe ranges [Closed]

> Username: liarokapisv  
> Created at: 2019-11-01 01:16:27 UTC  
> Updated at: 2020-10-17 04:11:09 UTC  
> Closed at: 2020-10-17 04:11:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76  

I have been experimenting with ranges using the trap policies. I have hit the following issue:  
  
```   
safe_unsigned_range<0, Max> x;  
safe_unsigned_range<0, Max> y;  
auto z = (x + y) / 2; // type is safe_unsigned_range<0, Max>  
auto k = x + (y - x)/2; // type is safe_signed_range<-Max/2, 3*Max/2>  
```  
In my mind the two expressions should mostly behave the same way. Unfortunately due to   
the type deductions the second expression is much harder to work with especially when using the trap policy.   
  
The problem boils down to the type system not identifying the same variables being reused in a single expression:  
```  
safe_unsigned_range<0, Max> x;  
safe_unsigned_range<0, Max> y;  
safe_unsigned_range<0, Max> z;  
auto k = x + (y - x)/2; //this is inherently more constrained than the following expression.  
auto l = z + (y - x)/2; // this has independent variables, deduced type is correct.  
```  
In my mind this requirement starts approximating symbolic manipulation and constraints. If we want to stay zero-cost it would require compile-time identifiable (eg can drive overloading) proxy objects per variable:  
  
```  
// []{} here is used to produce a unique type, can't think of a better solution.  
auto x_p = x.get_proxy([]{});   
auto y_p = y.get_proxy([]{});  
auto z = x_p + (y_p - x_p) / 2;  
```  
  
It should be possible to appropriately constraint the final expression with this information. A "simple" approach would be to use some metaprogramming to take advantage of the unique proxy-objects and simplify the expression so that it only contains independent variables, then we can fall back to the current deduction algorithm.   
  
Of course the machinary for this would be very complicated compared to the current deduction. I am also not convinced on the proper creation of such proxy objects. On the other hand if implemented, this allows to introduce arbitrary constraint solving, allowing for stricter compile-time analysis of ranges.  
  
Any thoughts?

---

## Comment 1

> Username: robertramey  
> Created at: 2019-11-01 04:52:57 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-548665790  

LOL - some very deep thinking going on here!  I can't respond without spending some significant time considering this.  But this doesn't inhibiting me from giving you some ideas.  
  
I think you might find interesting to look at how the library is implemented.  A key idea is that of what I call "extended integers".  Integers are well defined by abstract algebra.  It is a set with closed operations + and * which follow laws of associativity, commutativity, distributivity, etc.   So the result of applying these operations to a pair of integers is guaranteed to yield another integer.  It's a field.  
  
C/C++ defines integer.  It tries to mimic the algebraic concept because that is what we want to model.  But if fails to model it exactly.  C/C++ integer operations can overflow or fail in some other way.  So C/C++ integers do not form a closed set over operations +, *, etc.  So I defined another type of integers - "extended_integer".  Operations +,* form a closed set.  0,1,2, .... , 64k, overflowed.  And I extended operation + so that  overflow + overflow -> overflow.  So I defined a whole new arithmetic for integers.  This is similar to what the architects of C did for floating point numbers.  I never understood their motivation for doing this at the time, but now I see the merit of it.  So now we have a type of integer which works the same as floating point numbers do.  With constexpr this also works at compile time.  
  
safe<int> is built on top of extended integer.  
  
One of the weird things about this new type of integer is that operations are not associative !  BTW the same occurs with floating point operations.  That is if one has a sequence of binary operations the order one invokes them will alter the result!  Why is this OK?  Well it turns out that we inserted the trapping mechanism so that any operations which can't be guaranteed to be in the integer part of the result set are trapped.  
  
So ....  The order of operations can effect whether or not there will be an intermediate overflow.  Again this already happens with floating point operations - for the same reason.  They are both "extended numeric" types whose operations are not associative.  I don't see a general way to address this because the trapping will be depend on the sequence of operations AND the specific numbers involved - which are not known until run time.  But we can - and have - been able to guarantee that no result outside the the set of integers will be trapped.  At that point the user will be in a position to take appropriate measures such as reordering the operations, or using larger types or whatever.  
  
Note that this issue doesn't arise just with safe integers.  You might recall the problem of accumulating a long sequence of positive and negative floating point values.  If the sum is near zero, the result will be very imprecise.  The solution is to re-order the operations - see Kahan summing.  
  
Emulating integer arithmetic on a computer with a finite register width turns out to be a pretty deep philosophical problem - just as it is for floating arithmetic.   Turns out one can't just write down an arithmetic expression and guarantee that it will work.   I believe that I've proved that it will never be possible to this.  BUT with safe numerics we CAN guarantee that we will never fail to detect a failure.  To me this is a grand accomplishment.  Unfortunately, I've been unable to convince my colleagues of this.  Oh well.  
  
Good luck on this.

---

## Comment 2

> Username: liarokapisv  
> Created at: 2019-11-02 13:27:47 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-549043745  

Thanks Robert, your insight is much appreciated. I think I will work on writing a proof of concept, then we can experiment with this and see if it's worth implementing in the main library.  
  
Ps. I think the most minimal example of this behaviour is this:  
```  
std::safe_unsigned_range<0, Max> x = ...;  
auto y = x - x; // std::safe_signed_range<-Max, Max>  
```

---

## Comment 3

> Username: robertramey  
> Created at: 2019-11-02 14:16:17 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-549047745  

hmmmm - when I started the explanation above, I thought it was going to be a short observation.  But as I wrote, lots of stuff I went through came back to me.  It ended up being an argument for the view that there is only so much one can do.  When I first made the library it was sort of ad hoc in that I added code to to handle more and more cases.  This was the version that got accepted into boost.  But in trying to gather up loose ends, I found that this approach had a few problems:  
  
a) It was not "provable".  That is it had no underlying "truths" which were consistent through out the program.  The code, once written, could not really be explained or verified.  
b) there were lots of instances which would fail with one set of arguments and pass with others.  Fixing one case broke the other.  It became a of whack-a-mole.  
c) In many cases things like promotion rules could be set aside to get a "better" result.  But this  
broke the model of C++ integer arithmetic.  This model is an imperfect approximation to (mathematical) integer arithmetic. But it has a consistency about it.  I decided to implement the idea of "extended integers" to make C++ a mathematically correct structure.  This revealed the fundamental issue - lack of associativity.  Only under this view can one understand what one can and cannot do.  
  
Throughout this discussion the issue of constants hasn't been addressed.  But it has a huge impact!  Your example above would fail at compile time.  
  
I think your example above is exactly what the library should do.  
`x = x - x; // compile time error with trap policy  
also behaves as it should - as do  
`x--; x++;// ...'  
  
Consider a variation on your example above:  
`constexpr std::safe_unsigned_range<0, Max> x = ...;`  
`constexpr auto y = x - x; // result type depends upon ... above`  
  
There are big opportunities for improvement of the library, but I think your approach is a dead end.  
  
Here is what I'm working on:  
a) extending the library to floating point.  
b) extending the library to all "integer like" types.  That is those types which fullfill the requirements of the integer "concept"  
c) implement usage of "is constexpr value" so that one won't have to wrap constants in a macro to get their "constness" to propogate through expressions so that intermediate types are as small as necessary but no smaller.  
d) there are special "built-in" operations supported by clang and gcc which handle runtime trapping which use intrinsic operations of the intel processor. Incorporating these - correctly - would render many safe operations overhead free.  Along with c) above, a many "safe" programs would actually be faster than "unsafe" ones.  
  
To implement the above, the testing regimen would need to be taken to the next level - a considerable undertaking in itself.  
  
I appreciate your interest in the library.  I believe that eventually will become required practice in all environments which cannot tolerate bugs and undefined behavior.  That is in most real world programs.

---

## Comment 4

> Username: liarokapisv  
> Created at: 2019-11-02 16:54:02 UTC  
> Updated at: 2019-11-02 16:56:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-549061523  

I am sorry Robert, I think I don't understand your view about the above example:  
```  
std::safe_unsigned_range<0,Max> x;  
auto y = x - x;  
```  
Currently the above example compiles fine and deduces the type of `y` as `std::safe_signed_range<-Max,Max>`. I don't agree that this is the correct behaviour. Or rather I do think that it's the correct behaviour but I find it unsatisfactory. You most likely won't be able to use the resulting `y` above later in the program due to not being able to restrict it to a sane range. Ideally we would like `y` to be deduced either as `safe_[un]signed_range<0,0>` or `safe_[un]signed_literal<0>`.   
The library attempts to deduce the correct range but turns out to be very lax about it.   
  
I also don't understand your counter-example above.  
```  
x = x - x; // compile time error with trap policy also behaves as it should  
```  
Why should this produce a compile-time error? If `x` has the type `std::safe_unsigned_range<0,Max>` then the only possible resulting value of the above subtraction is `0` which is a valid value of `x`. This is statically-provable. So infact there should not be a compile time error here. This is a good example of why the current library behaviour is unsatisfactory.  
  
Don't forget that I am not suggesting to change any of the library's current behaviour. What I am suggesting is adding a new feature to make the type system more aware of each variable's identity. This does not have to be perfect but we should be possible to work with the safe_ranges without having to resort to casting to unsafe types to perform some calculations due to the deductions not being strict enough.  
  
I also understand that implementing this may be very problematic but I think that it's a goal that the library should try to attain at some point. Otherwise safe_ranges are just not very usable in practice except as api-boundary types.

---

## Comment 5

> Username: robertramey  
> Created at: 2019-11-02 22:24:03 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-549086597  

`x = x - x; // compile time error with trap policy also behaves as it should`  
  
I produces a compile time error be case the result is <-MAX, MAX> which can't be guaranteed to be storable in to a <0, Max>.    
  
But now you point out to me that the range of x - x => 0,0 it should always pass.  Ideally on should be able to detect this at compile time and just force a <0,0>.  Off hand, it's not obvious to me how to do this.  But I'll look into to it.  But now I see your point.  
  
> I also understand that implementing this may be very problematic   
  
looks like it  
  
> but I think that it's a goal that the library should try to attain at some point.  
  
sure if one can figure out how to do it.  
  
> Otherwise safe_ranges are just not very usable in practice except as api-boundary types.  
  
I don't know about that.  It's hard to know without real experience.  Note that using is constexpr evaluated will permit all const values to be transformed to safe_range<const value, const value> which will have a big impact.

---

## Comment 6

> Username: liarokapisv  
> Created at: 2019-11-03 12:08:55 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-549130240  

> I don't know about that. It's hard to know without real experience.  
  
What I am saying is that due to the lax deduction one has to change the expressions  
so that each variable is only encountered once in them. This creates readability issues and is not always possible which makes using safe_ranges a bit awkward. I was most definitely a bit harsh on my wording there.  
  
> Off hand, it's not obvious to me how to do this.   
  
The problem is that currently the type system can't distinguish between two separate variables with the same type and the same variable encountered twice. One solution to this is to create proxy objects, wrappers around the variables, each with a distinct type. Something like this:  
  
```  
template <class T, class UniqueType>  
struct proxy;  
  
template <class T, class UniqueType>  
proxy<T, UniqueType> make_proxy(T, UniqueType);  
  
std::safe_unsigned_range<0, Max> x;  
std::safe_unsigned_range<0, Max> y;  
auto x_p = make_proxy(x, []{});  
auto y_p = make_proxy(y, []{});  
```  
Notice that while `x` and `y` above are not distinguisable in an overload context,  
`x_p` and `y_p` are distinguisable due to each having a unique type. The lambdas are used as a unique type generator to ensure that each proxy object has a unique type. The unique_type template parameter can be used as some sort of Id to take advantage when deducing the type of the expression so as to detect multiple occuring variables.  
  
Obviously `make_proxy` above must always be supplied with a unique type. The fact that we are using lambdas there is an implementation detail,I think the best approach would be to wrap that in a macro athough I don't like it:  
  
```  
#define PROXY(x) make_proxy(x, []{})  
...  
auto x_p = PROXY(x);  
auto y_p = PROXY(y);  
```  
This approach requires the whole expression to be visible to the deduction mechanism meaning that we have to lazy evaluate the expression. Using proxies would most likely require forcing evaluation when used:  
  
```  
safe_unsigned_range<0, Max> x;  
auto x_p = PROXY(x);  
// res1 has some internal AST type.  
auto res1 = x_p - x_p;  
// the AST is traversed, multiple occuring variables handled,  
// safe_unsigned_literal<0> , safe_unsigned_range<0,0> deduced.  
auto res2 = eval(x_p - x_p);   
```  
  
I hope this at least gives some insight on how the problem can be solved.   
It's basically:  
- Create proxy objects to distinguish objects of the same type in overload context  
- Produce compile-time AST of the expressions containing proxy objects  
- Deduce the resulting type by evaluating the AST at compile-time.  
  
I think the biggest problem lies in the last step.   
That step basically translates to the following problem statement:  
  
> Given a set of symbols representing integers in a range, find the minimum and maximum values of the given symbolic expression composed of the basic arithmetic operations.  
  
Then there is the matter of solving the above problem efficiently at compile-time. Obviously one can't solve this in a brute force way as it would make the compilation times prohibiting.   
  
It should however be possible to do this efficiently for specific sets of expressions although I am not sure if this is possible in the generic case. I think the library does not have to deal with extremely complicated expressions anyway but that remains to be seen.  
  
As you can see while possible to implement, this whole thing requires quite some complicated machinary. Moreover this is only to help users not have to factor their expressions manually which inhibits readability.   
  
Is it worth it? I am not sure but I think it's worth examining.

---

## Comment 7

> Username: robertramey  
> Created at: 2020-10-17 04:11:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/76#issuecomment-710744011  

closing this - feel free to re-open if necessary.
