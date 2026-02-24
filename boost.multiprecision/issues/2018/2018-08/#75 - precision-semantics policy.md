# #75 - precision-semantics policy [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-18 20:11:41 UTC  
> Updated at: 2018-09-27 16:50:51 UTC  
> Closed at: 2018-09-27 16:50:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75  

*edit:* i changed the title of this issue from "writing a complex class using `mpfr_float` that is precision-semantically equivalent to the new `mpc` type".  
  
within the Boost.Multiprecision library, there are some precision rules:  
  
- Copying or move-assignment copies the precision of the source.  
- Assignment keeps the precision of the target.  
  
for legacy purposes, i have to keep supporting users of my library with Boost versions that do not contain the new `mpc` implementation of complex numbers.  i want my type to be precision-semantically identical with `mpc`.  i am struggling with one thing, and, while i know this isn't a Boost.Mutiprecision issue *per se*, since it is a use of bmp, i am hoping for some help.    
  
if this is too off-topic, please **politely** decline -- i'm feeling anxious about asking this already, admitting that i am ignorant about this; i'm a mathematician first and a programmer second.  
  
---  
  
my class is the "obvious" implementation of complex numbers with a `bmp::mpfr_float` as the real and imaginary fields, and all the typical operator overloading, etc.  it's not elegant but it works.  
  
my one and only family of failing tests relate to mixed precision arithmetic.  when i take complex's `a` and `b`, at different precisions, and say add them, stuffing them into a previously existing `z` at some other precision, the `mpc` policy is that `z` should be at its same precision after the arithmetic.  however, with RVO and move assignment in the mix, **my** `z` ends up at whatever precision the result is at.  i can fix this by making the move assignment operator preserve precision, but this breaks moving.  no good.  i feel like i need to distinguish between moving and these cases coming from arithmetic, but that it's an impossible problem without re-writing the whole thing using ET's and basically replicating Boost.Multiprecision, a non starter.  
  
here's the test that fails **for my naive implementation**, whereas this test passes for the glorious new `mpc` implementation:  
```  
BOOST_AUTO_TEST_CASE(complex_precision_predictable_add)  
{  
	DefaultPrecision(30);  
	bertini::mpfr_complex a(1,2);  
  
	DefaultPrecision(50);  
	bertini::mpfr_complex b(3,4);  
  
	DefaultPrecision(70);  
	bertini::mpfr_complex c(5,6);  
  
	a = b+c;  
	BOOST_CHECK_EQUAL(Precision(a),30);  
  
	DefaultPrecision(90);  
	a = b+c;  
	BOOST_CHECK_EQUAL(Precision(a),30);  
}  
```  
  
my obvious overload for `+`:  
```  
inline custom_complex operator+(custom_complex const& lhs, const custom_complex & rhs){  
		return custom_complex(lhs.real()+rhs.real(), lhs.imag()+rhs.imag());  
	}  
```  
  
should i give up and just accept that because of RVO and move assignment that, without expression templating my type and basically recreating Boost.Multiprecision itself at some level, this is an impossible problem to solve?  
  
my genuine thanks for any consideration.   🙇‍♀️

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-21 12:12:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-414652508  

I think you're stuck here - in fact Multiprecision is deeply schizophrenic over this, and in general you absolutely cannot reason over how many temporaries will be created, or whether they are default constructed (current default precision) or copy constructed (copies source precision).  So even in the simplest possible case:  
```  
z = x + y;  
```  
We have two options:  
  
1) Expression templates are on - then we end up with a direct mpc_add using z as target and no temporaries.  Result is the precision of z under mpc/mpfr's rules.  
2) Expression template are off - then we end up default constructing one temporary adding the result into it, and then move assigning that to z.  Result is at current default precision.  
  
For more complex expressions things get even worse because the expression template may radically reorder operations, change them even from operator+ to a += etc.  
  
The only conceivable way I can think of to ensure complete sanity here, is to always promote to the highest precision of each the arguments (rather like arithmetic type promotion), and return to assignment-copies-precision semantics.  But then you would loose the ability to automatically up/down-sample precision on assignment, and instead would have to use  
```  
x = std::move(mpc_float(y, new_precision));  
```  
etc, even though x may be at the precision you want prior to the assigment.  
  
On the other hand, perhaps this is worth it for better sanity elsewhere?

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-08-21 17:22:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-414754177  

### assignment preserves precision of target (APPoT)  
  
I like consistency.  I like how predictable things are with `et_on`, getting the existing precision upon assignment in all cases.  That was easy to write test cases against, and for the precision policy is consistent with the underlying libraries.  That's sane.  And, based on my experience with mpfr in C, this is actually what I expected.  Setting/assignment preserves precision of target; precision-changing is explicit.  
  
### assignment preserves precision of source (APPoS)  
  
OTOH, `x = std::move(mpc_float(y, new_precision));` is not terrible in my mind; in fact, i kinda like it.  i would primarily use it at points in my flow where a precision change is required, and more or less only there, assuming that precision is uniform -- and working hard to keep it so.  Perhaps losing automatic up/down-sampling is an acceptable sacrifice to gain consistency between `et_on` and `et_off`?  
  
---  
  
💡in a related vein, what I think might really help is to provide a switch that one can compile-in that prohibits mixed precision arithmetic -- it throws, say.  maybe `BOOST_MULTIPRECISION_FORBID_MIXED_PRECISION_ARITHMETIC` or something less verbose but equally as clear.   
 Then, I can diagnose any problems that come from it at the source.  I think this would be useful regardless of any policy decisions about precision on assignment.  
  
Another question I have: are there any performance concerns with APPoT/APPoS?   
Unsurprisingly, this `mpc_complex` will become [is now, in fact] central to my library, so any little things will build up to make a real difference in performance of my code.  I would definitely sacrifice consistency between `et_on` and `et_off` to gain some performance.  
  
I don't know, is what I am trying to say.  I want to help bring predictable behaviour that I can communicate to my users, and will never change.  I think we (you) get to choose, but that choice should be careful, deliberate, and well-documented, esp. because of the centrality of Boost to many C++ developers (me!).    
  
If poked harder to make a decision *this instant*, I would take APPoT for consistency with dependencies and the pleasure of my experience writing code against `mpc_complex` heretofore, and deal with the insanity by never allowing `et_off`, requiring ≥ the Boost version that contains these updates (hopefully 1.69), and fearlessly writing my code knowing APPoT.  I also know that versus `et_off` for `mpfr_float` with my old custom complex type wrapped around it, `et_on` gives me about 20% better performance.  And that was just with ET's for the underlying reals!  So ET's for complexes will have an even bigger impact, I predict.  So there's, like, no reason to use `et_off` in my mind.   
 Maybe this is crap reasoning, though?  That's why I am having this conversation with you!  
  
Thanks for the conversation, I **really** appreciate your time.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-08-21 18:44:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-414780913  

I don't know what the right thing to do is either.  
  
I'm not sure that expression templates are as consistent as you think - for complex expressions there will still be temporaries created at uncertain (ie default) precision.  
  
If we use APPoS then there is a performance hit - a small one checking whether we need to change the precision of the target, and then a bigger one if the target (or any temporaries) have to be "re-precisioned".  
  
I'm wondering if the answer is "don't do that", and provide tools for the user to check at runtime that they're not accidentally doing mixed-precision arithmetic?  That would include having the current default precision the same as the arguments so that any temporaries have the right precision.  It would be nice if temporaries could be magically constructed at the correct precision, but the number of places that need checking/fixing is crazy and I simply don't know how to validate that :(

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-21 18:57:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-414784723  

Ugh, debug checks don't enforce consistent behaviour either - not unless you enforce uniform precision is assignment and move assignment as well - and that would prohibit a whole bunch of use cases :(

---

## Comment 5

> Username: ofloveandhate  
> Created at: 2018-08-21 19:29:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-414793816  

I think you're right; there's little that can be done about generating temporaries at default precision.   
  
We must therefore write code that changes precision deliberately and cautiously.  For example, I keep banks of complexes around and change them all at certain points in my code to resolve this problem, and then just assume that precision is uniform, for better or worse.  Or, I take a collection of vectors, say, get the highest precision of any of them, upsample them all to that, change default, and then proceed knowing that I am consistent. A library-level runtime consistency check would be *awesome*, but I understand that might be unwieldy to implement.  Even a few opportunities or tools to that end would be better than none, though!  Maybe that's a longer-term goal?  
  
I currently vote for APPoT, based on performance and consistency with mpfr and mpc (with `bmp::et_on`), where committing to this policy for me would mean that I entirely ditch my old custom complex, latch closely onto current BMP,  and embrace only precision-semantically compliant types to `mpc_complex<et_on>` forever, til ☠️ do we part.  this would also resolve #76 for me.  
  
I think that I should try to compile my Python bindings and ensure that APPoT for `mpc` is carried through. (this is not necessarily a trivial task since they haven't been ported to `mpc_complex` yet, but won't take me too too long, hopefully between a few hours and a week).    
  
Maybe we should take a break to think about APPoT / APPoS before *really* committing?  Or ask another person?  Does Christopher Kormanyos or another stake holder have an opinion?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2018-08-22 18:29:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-415132791  

I've just posted to the develop mailing list on this...

---

## Comment 7

> Username: ofloveandhate  
> Created at: 2018-08-22 22:24:08 UTC  
> Updated at: 2018-08-22 22:25:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-415201681  

I checked [my Python bindings](https://github.com/bertiniteam/b2/tree/develop/python).  Things work sanely, with `et_on` and APPoT being tested.  I am now comfortable embracing the APPoT paradigm, as it works consistently in my two main projects.  
  
👍   
---  
  
For posterity, here's the code:  
```  
def test_mp_complex_precision(self):  
    mp.default_precision(30);  
      
    x = mp.Complex(1);  
  
    mp.default_precision(40);  
  
    y = x;  
  
    a = mp.Complex(4)  
    b = mp.Complex(5)  
  
    self.assertEqual(y.precision(),30)  
  
  
    mp.default_precision(50);  
    z = mp.Complex(3);  
      
  
    mp.default_precision(60)  
  
    c = mp.Complex(6)  
    d = mp.Complex(7)  
    w = x+y  
    self.assertEqual(w.precision(),60)  
  
    c = a  
    self.assertEqual(c.precision(),40) # even though the source is 40, target is 60, and APPoT.  
    d = a+b  
    self.assertEqual(d.precision(),60) # even though the source is 30, target is 60, and APPoT.  
```

---

## Comment 8

> Username: ofloveandhate  
> Created at: 2018-08-28 01:44:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-416423345  

i feel like one strike against APPoT is that it masks precision problems 🎭 , in that you never see from the outside that non-target precision may have been used; the target is guaranteed to be in its starting precision.  i still like APPoT, though, over APPoS.

---

## Comment 9

> Username: ofloveandhate  
> Created at: 2018-08-30 15:47:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417368024  

any word back from the dev list?

---

## Comment 10

> Username: jzmaddock  
> Created at: 2018-08-30 17:03:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417393514  

Yes, but I'm not sure whether it helps:  
  
"How about something like this:  
- Assignment always preserves the precision of the source.  
- Mixed precision arithmetic uses the maximum precision of  
  any argument.  
- There is an explicit function for setting the precision.  
- There is an way to force assignment to use the precision  
  of the destination, but it must be explicit in some way.  
  
I believe these are the correct semantics, without  
regard for performance.  So, to get performance back:  
- When using expression templates and the precision of the  
  result is set, the precision of intermediate results  
  may be reduced as long as it does not change the final  
  result too much.  (The definition of "too much" is  
  left as an exercise for the reader)."

---

## Comment 11

> Username: ofloveandhate  
> Created at: 2018-08-30 17:29:15 UTC  
> Updated at: 2018-09-04 21:11:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417401703  

interesting, the suggestion is for APPoS.  i do like APPoS, because it is easier to tell if something is out of precision.  i just don't have a strong preference.  
  
> * Assignment always preserves the precision of the source.  
  
easy, right? 🤷‍♀️  is there much change needed with `et_on` to get APPoS working?  not trying to engage in a sunk cost fallacy here, just trying to understand feasibility.  
  
> * Mixed precision arithmetic uses the maximum precision of  
any argument.  
  
this requires modification of the internals of the library, right?  
  
> * There is an explicit function for setting the precision.  
  
this is already done, just `z.precision(##)`  
  
> * There is an way to force assignment to use the precision  
of the destination, but it must be explicit in some way.  
  
i think this is just `z.assign(x+y*q,60)`, right?  
  
> I believe these are the correct semantics, without  
regard for performance. So, to get performance back:  
>   
> * When using expression templates and the precision of the  
result is set, the precision of intermediate results  
may be reduced as long as it does not change the final  
result too much. (The definition of "too much" is  
left as an exercise for the reader)."  
  
I think this person is thinking of the precision cost due to higher precision, not that of constantly checking precisions of things as came up earlier in this discussion.   Is this worth it?  I personally strive to use uniform precision, because there is [numerical analysis underlying the precision changes in my code](https://www3.nd.edu/~jhauenst/preprints/bhswAMP2.pdf).  the minimum working precision at any point in the code is based on the (log10 of the) condition number of the jacobian.  so, i am not sure i care about performance in mixed precision case, which would come up inbetween primary loops.  but i do care about performance *in general*, because complex variable precision arithmetic **is** the critical pathway for me.    
  
one other concern I have:  
if APPoS is the final choice (not saying it is yet), there needs to be clear documentation about resulting precisions of targets.  that is, if i go prec30*prec60, what's the result at?   30?  60?  and surely it's commutative?

---

## Comment 12

> Username: jzmaddock  
> Created at: 2018-08-30 19:07:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417432913  

>>        Assignment always preserves the precision of the source.  
  
>easy, right? 🤷‍♀️ is there much change needed with et_on to get APPoS working? not trying to engage in a sunk cost fallacy here, just trying to understand feasibility.  
  
Very little change IMO.  There might be some messing about with assignment of expression templates to stop them from using a lower precision target variable as working space.  
  
And it is potentially a saner alternative - or at least more consistent?  
  
>>        Mixed precision arithmetic uses the maximum precision of any argument.  
  
>this requires modification of the internals of the library, right?  
  
Getting mixed precision arithmetic to work correctly with variable precision types requires a very significant investment of time regards of the model used.  
  
  
>>        There is an explicit function for setting the precision.  
  
>this is already done, just z.precision(##)  
  
Yep.  
  
>>        There is an way to force assignment to use the precision  of the destination, but it must be explicit in some way.  
  
>i think this is just z.assign(x+y*q,60), right?  
  
Yep.  
  
>>    I believe these are the correct semantics, without regard for performance.   
  
Which is probably true BTW.  
  
>I think this person is thinking of the precision cost due to higher precision, not that of constantly checking precisions of things as came up earlier in this discussion. Is this worth it? I personally strive to use uniform precision, because there is numerical analysis underlying the precision changes in my code. the minimum working precision at any point in the code is based on the (log10 of the) condition number of the jacobian. so, i am not sure i care about performance in mixed precision case, which would come up inbetween primary loops. but i do care about performance in general, because complex variable precision arithmetic lives is the critical pathway for me.  
  
If you always keep precisions consistent, then this should all be a non issue anyway.  But to operate at a lower precision, you would have to copy all the input variables, do the calculation and then move back to higher precision.  
  
My principal concern is for the person who has a huge matrix of high precision data, and wants to do a low precision computation on it - under this model they can't - because as soon as they perform an arithmetic operation involving the data, it's performed at that higher precision and then assigned to the result.  
  
In other words there's no support for "add_at_precision(x, y, N)" that performs N-digit addition of x and y regardless of how many digits x and y have.  
  
However, this model does closely mimic what happens with mixed arithmetic of fixed precision types.  
  
>one other concern I have:  
>if APPoS is the final choice (not saying it is yet), there needs to be clear documentation about resulting precisions of targets. that is, if i go prec30*prec60, what's the result at? 30? 60? and surely it's commutative?  
  
The only sane choice under this model is 60, and yes commutative.  
  
Crazy question - do you primarily operate at just 2 precisions?  And if so have you considered using fixed precision types?  You could ditch memory allocation altogether on new variable construction as well - at least for mpfr (sadly not mpc).

---

## Comment 13

> Username: ofloveandhate  
> Created at: 2018-08-30 19:17:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417435872  

> Crazy question - do you primarily operate at just 2 precisions? And if so have you considered using fixed precision types? You could ditch memory allocation altogether on new variable construction as well - at least for mpfr (sadly not mpc).  
  
no; well, kinda.  i use hardware double when possible, and multiple when dictated.  one feature of the software is that we let people choose adaptive, or a fixed precision.  adaptive is where it's at, though, for actually solving non-trivial systems.  
  
the thought has gone through my mind to use a fixed set of precisions known at compile time, and move between them as needed.   one package (not c++), [homotopycontinuation.jl](https://github.com/JuliaHomotopyContinuation/HomotopyContinuation.jl), is thinking of taking something like this approach, but they will limit themselves on what problems they can solve without arbitrary precision.  they also had only implemented double precision stuff last i talked to them, so this will be a long bridge to cross for them, i bet.  i personally still like mpfr-style variable precision.

---

## Comment 14

> Username: ofloveandhate  
> Created at: 2018-08-30 19:30:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417439770  

> But to operate at a lower precision, you would have to copy all the input variables, do the calculation and then move back to higher precision.  
  
i thought ^^^ this was par for the course?  
  
> My principal concern is for the person who has a huge matrix of high precision data, and wants to do a low precision computation on it - under this model they can't - because as soon as they perform an arithmetic operation involving the data, it's performed at that higher precision and then assigned to the result.  
>   
> In other words there's no support for "add_at_precision(x, y, N)" that performs N-digit addition of x and y regardless of how many digits x and y have.  
  
can we brainstorm this problem?    
  
let's suppose we have `x`, `y`, matrices of data at precision 1000, and a matrix `t` at precision 100.   
 are you suggesting that with APPoT, `t=x+y` would yield arithmetic at precision 100?  my naive perspective guesses that the arithmetic happens at precision 1000, then is downsampled to fit into `t`.  is this not the case?  i have basically been operating at the assumption that if i want arithmetic to happen at some precision, i need to have operands at that precision already.  
  
oooh, i think you mean that `add_at_precision(x, y, N)` is as-if  
  
```  
template <typename T>  
T add_at_precision(T x, T y, digits10 N)  
T x_other(x,N);  
T y_other(y,N);  
T result = x_other+y_other;  
result.precision(N);  
return result;  
```  
am i on the right track?

---

## Comment 15

> Username: jzmaddock  
> Created at: 2018-08-31 12:01:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417642997  

>> But to operate at a lower precision, you would have to copy all the input variables, do the calculation and then move back to higher precision.  
  
>i thought ^^^ this was par for the course?  
  
Sort of, but consider mpfr C code first, if we do a:  
  
```  
mpfr_mul(dest, a, b, GMP_RNDN);  
```  
  
The I assume that mpfr will do just enough work to obtain a correctly rounded result at the precision of `dest` regardless of the precision of a and b (which may be much higher precision).  The mpfr manual is a bit vague on this so maybe I'm deluding myself?  
  
Now, if expression templates are on and we do a:  
  
dest = a * b;  
  
we end up with exactly the same code path as the C code above, no temporaries, just a straight mpfr_mul into the result.   So, quite by accident, it's fast as long as APPoT is what you want.  Trouble is, it's not currently predictable once more complex expressions involving temporaries are involved, and/or you start getting move semantics kicking in.  So as things stand the only thing we can say about:  
  
```  
dest = complex_expression;  
```  
  
Is that the precision of the result is unspecified unless all variables (both source and destination) have the same precision as the current default.  I suspect it's going to be very hard to say much else if we stick with  APPoT.  But with APPoS it's a bit easier to implement something sane because each and every operation operates at the highest precision of the source variables involved.

---

## Comment 16

> Username: jzmaddock  
> Created at: 2018-08-31 12:05:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417643771  

>let's suppose we have x, y, matrices of data at precision 1000, and a matrix t at precision 100.  
are you suggesting that with APPoT, t=x+y would yield arithmetic at precision 100? my naive perspective guesses that the arithmetic happens at precision 1000, then is downsampled to fit into t. is this not the case? i have basically been operating at the assumption that if i want arithmetic to happen at some precision, i need to have operands at that precision already.  
  
Matrix arithmetic is a tricky one because it would depend on the internals of the matrix implementation.  If everything is expression templates, then  
  
```  
a = b + c;  
```  
  
Might well operate at the precision of `a` as things stand (and if my understanding of mpfr is correct).  Multiplication is a whole other ball game, because there are likely to be any number of temporaries involved in the calculation.

---

## Comment 17

> Username: jzmaddock  
> Created at: 2018-08-31 12:10:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417644750  

>oooh, i think you mean that add_at_precision(x, y, N) is as-if  
  
I was thinking that we could provide non- member functions which generate the target at a precision other than the largest of the sources.  So I naive form would be:  
  
```  
template <typename T>  
T add_at_precision(T x, T y, digits10 N)  
{  
T x_other(x,N);  
T y_other(y,N);  
T result = x_other+y_other;  
return result;  // result is already at precision N regardless of default.  
}  
```  
  
IFF I'm correct about MPFR then a better implementation wouldn't need to create temporaries and would just do an mpfr_add direct to `result` with `result` constructed at the desired precision.  But of course you can only use this in code that you write, not inside say third party matrix arithmetic.

---

## Comment 18

> Username: jzmaddock  
> Created at: 2018-08-31 15:50:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417707181  

I think my main reservation about APPoS is that just a few high precision values can pollute the whole computation - consider a matrix multiplication `a = x * y` where basically every value gets permuted with every other value - if there are just a few high precision values in either of x or y, then the whole of `a` ends up high precision.  But.... it is in some senses the "correct" thing to do, and easy to reason about.  
  
Potentially, the optimal solution would be a policy based approach whereby we have a thread-local policy in effect that determines the behaviour, so this might include:  
  
1) Don't care: this is what we have now.  All checks would evaluate to no-ops, and it's up to the user to make sure everything is consistent.  
2) APPoS: maybe the default?  Consistent and always numerically correct, but maybe slower as well.  Both temporaries and result are at the precision of the highest precision Source operand.  
3) APPoT: all temporaries would be at the precision of the Target as would the result.  Probably need to make move-assign APPoT as well.  
4) Checked: as discussed above, hard to implement correctly unless you also ban mixed assignment/copy, could be useful for debugging (1) though.  
5) Current default: all temps and result are at the current working precision.  
  
Anything else?  
  
The only thing I can think of that would be incompatible with this, are parallel implementations of the arithmetic operations.  But we don't have those ;)  
  
That would allow you I think, to temporarily change the default behaviour, and say "multiply these two matrixes and have the result and all intermediates at current default precision please", and all up/down sampling would "just happen".

---

## Comment 19

> Username: ofloveandhate  
> Created at: 2018-08-31 16:43:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417722991  

i figured i'd bring in a comparison with another library...  here's what a "competitor" to Boost.Multiprecision, [mpreal](http://www.holoborodko.com/pavel/mpfr/) by Pavel Holoborodko, says about their code (which doesn't look maintained by the website):  
  
> Main MPFR C++ rule for intermediate results is prec(rop) = max(prec(op1),prec(op2)): result of operation is stored with the max of the precisions of operands.  
>   
> In other words intermediate operations are conducted with the reasonably maximum precision defined by precisions of arguments and doesn’t depend on precision of target variable (in our example – x).  
>   
> This is the most natural rule dictated by practical applications, such as numerical differentiation, digital filter convolution, etc. where in order to obtain correct and accurate final result intermediate calculations have to be done with high accuracy.  
>   
> Other libraries calculate intermediate results with the precision of final variable or round them to some not obvious to user precision which is independent from precision of arguments as well as from precision of final variable. This could lead to a significant reduction in the accuracy of the final result.  
  
what wisdom can we glean from this description?

---

## Comment 20

> Username: ofloveandhate  
> Created at: 2018-08-31 16:50:40 UTC  
> Updated at: 2018-08-31 16:52:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417725508  

> I think my main reservation about APPoS is that just a few high precision values can pollute the whole computation - consider a matrix multiplication `a = x * y` where basically every value gets permuted with every other value - if there are just a few high precision values in either of `x` or `y`, then the whole of `a` ends up high precision. But.... it is in some senses the "correct" thing to do, and easy to reason about.  
  
this seems like the correct thing to me... the parts of `a` in the same row or column as a high-precision entry in `x` or `y` should probably drift higher in precision.    
  
this whole conversation is filled with back and forth in my mind.  wow.  i just can't stay focused on APPoT or APPoS.  they're both so reasonable in their own ways.  
  
> Potentially, the optimal solution would be a policy based approach whereby we have a thread-local policy in effect that determines the behaviour, so this might include:  
> ... (list omitted from quote)  
  
i like the policies ideas.  this was in my head earlier in the discussion, to add a policy choice.  "prefer explicit over implicit", i teach my students.  perhaps it's the best solution to this problem -- pass the choice on to the user.

---

## Comment 21

> Username: ofloveandhate  
> Created at: 2018-08-31 16:58:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417727891  

i note that i think it is hard to write client code that doesn't care about the policy choice.  APPoS seems to induce a "change precision of operands early" and "change precision of targets late" kind of code, while APPoT feels like "just maintain precision of target and it'll be fine".    
  
 i know about only my own subject area, so...  i don't know how other people feel about this.  how does this jive with you?

---

## Comment 22

> Username: ofloveandhate  
> Created at: 2018-08-31 17:47:34 UTC  
> Updated at: 2018-08-31 17:48:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417740984  

from [the official mpfr documentation](https://www.mpfr.org/mpfr-current/mpfr.html):  
  
(from 4.4, rounding modes)  
>  MPFR behaves as if it computed the result with an infinite precision, then rounded it to the precision of this variable. The input variables are regarded as exact (in particular, their precision does not affect the result).  
  
(from 5, mpfr interface)  
> The user has to specify the precision of each variable. A computation that assigns a variable will take place with the precision of the assigned variable; the cost of that computation should not depend on the precision of variables used as input (on average).  
>   
> The semantics of a calculation in MPFR is specified as follows: Compute the requested operation exactly (with “infinite accuracy”), and round the result to the precision of the destination variable, with the given rounding mode. The MPFR floating-point functions are intended to be a smooth extension of the IEEE 754 arithmetic. The results obtained on a given computer are identical to those obtained on a computer with a different word size, or with a different compiler or operating system.  
>   
> MPFR does not keep track of the accuracy of a computation. This is left to the user or to a higher layer (for example the MPFI library for interval arithmetic). As a consequence, if two variables are used to store only a few significant bits, and their product is stored in a variable with large precision, then MPFR will still compute the result with full precision.  
  
so, it really looks like MPFR is APPoT in flavor, we we noted earlier in this issue.  of course, there are a bunch of options for arithmetic in MPFR.

---

## Comment 23

> Username: jzmaddock  
> Created at: 2018-08-31 18:11:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417747789  

I confess I don't use the variable precision types, just the fixed precision ones...

---

## Comment 24

> Username: ofloveandhate  
> Created at: 2018-08-31 19:02:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417761282  

that's fine!  all are welcome!

---

## Comment 25

> Username: jzmaddock  
> Created at: 2018-09-01 17:43:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417875862  

I've been reflecting on this a bit, and I think that APPoS is probably the only option I can implement consistently between expression templates on and off.  Well that and "don't care", but that's rather less useful!

---

## Comment 26

> Username: ofloveandhate  
> Created at: 2018-09-02 01:55:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417899305  

Ok, APPoS seems like a good conclusion to come to.  🌸 i have a bunch of tests and code that are written against APPoT, so I'll change them.      
  
I wish I were stronger at the core stuff you do with this wonderful library, so I could contribute more than filing issues and conversing! yet, i am glad that I get to participate in this process with you.  thanks for including me.

---

## Comment 27

> Username: jzmaddock  
> Created at: 2018-09-02 17:45:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-417947197  

OK let me see what I can do...

---

## Comment 28

> Username: jzmaddock  
> Created at: 2018-09-06 19:40:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-419216939  

APPoS implemented in https://github.com/boostorg/multiprecision/pull/82 includes mixed precision arithmetic support.  Big intrusive change, so it's a PR for now while CI cycles.  Currently lacking decent tests (suggestion or code welcome!).  
  
I'm away on holiday shortly so I'll deal with this when I get back.

---

## Comment 29

> Username: jzmaddock  
> Created at: 2018-09-16 18:22:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-421808061  

Merged to develop.

---

## Comment 30

> Username: ofloveandhate  
> Created at: 2018-09-16 20:58:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-421832351  

i want to do more testing, but it'll be a little bit.  thanks!!!

---

## Comment 31

> Username: ckormanyos  
> Created at: 2018-09-17 19:49:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-422146576  

In the past for other projects, I've done the following...  
* Allow the user to select precision before each big operation(such as multiplication or difvision).* Internal operations such as inversion, square root, andelementary transcendental functions set the neededprecision at every step of the calculation, such as in a Newpton-Raphsoniteration or series calculation. This makes the precision issuemostly efficient and hidden from the user.  
As John indicated, our focus has been primarily on thefixed precisioncase and we try to support this withgreat correctness.  
The issue of carrying only the neede precision is, however,one that we might consider more closely in the future.  
In the larger sense, the topic of exactly how to handle precisionfor calculations with big´number types is ultimately somethingthat would need to be specified with great care. So sure,we should definitely be discussing this issue. But we must becautious and avoid breaking any of Multiprecision in its present form.  
With kind regarss, Chris  
   On Sunday, September 16, 2018, 10:58:24 PM GMT+2, danielle brake <notifications@github.com> wrote:    
   
   
i want to do more testing, but it'll be a little bit. thanks!!!  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 32

> Username: jzmaddock  
> Created at: 2018-09-19 14:13:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-422820013  

>In the larger sense, the topic of exactly how to handle precisionfor calculations with big´number types is ultimately somethingthat would need to be specified with great care. So sure,we should definitely be discussing this issue. But we must becautious and avoid breaking any of Multiprecision in its present form.  
  
The conclusion I came to, was that this behaviour was the only thing we could implement reliably that gave predictable results in the presence of both rvalue moves and expression templates, either one of which could unpredictably "bump up" your precision to the same policy as this at any moment anyway.  
  
Newton iteration is a good test case,  I'll try and whip up an example.

---

## Comment 33

> Username: jzmaddock  
> Created at: 2018-09-20 07:57:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-423081009  

FYI there are still issues with mixed precision and expression templates... much harder to get right than I thought :(

---

## Comment 34

> Username: jzmaddock  
> Created at: 2018-09-22 08:40:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-423727897  

The issues should be fixed now, also added a variable precision Newton Iteration example.  Performance gain from using "just enough" precision at each step seems to be about 4-5x.

---

## Comment 35

> Username: jzmaddock  
> Created at: 2018-09-27 16:50:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/75#issuecomment-425164556  

Closing down for now, please file any issues found as new bugs - thanks!
