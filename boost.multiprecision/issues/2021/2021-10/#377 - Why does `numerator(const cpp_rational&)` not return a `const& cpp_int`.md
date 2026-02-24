# #377 - Why does   `numerator(const cpp_rational&)`  not return a `const& cpp_int` [Open]

> Username: afabri  
> Created at: 2021-10-07 11:38:30 UTC  
> Updated at: 2023-02-10 17:11:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377  

I would expect not get a copy. Is the rational number more than just two integers?

---

## Comment 1

> Username: afabri  
> Created at: 2021-10-11 12:06:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-939969087  

Would it make sense to have a traits class which tells us that it is const&   or a copy?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-11 12:19:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-939978395  

Not really, as of current develop, none of the rational types store pairs of `number<>` to which we could return a reference.  Some might be able to be type-punned to such a type, but it would be deeply risky IMO.  Others like mpq_rational definitely don't have anything we can return a reference to.

---

## Comment 3

> Username: mglisse  
> Created at: 2021-10-18 18:48:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-946063268  

GMPXX's `mpq_class::get_num` does the "deeply risky" `reinterpret_cast` and seems to get away with it (possibly through a mix of 1- people not writing weird code, 2- often casting to the underlying safe type before using the object, 3- compilers not relying on types when addresses are obviously the same, and 4- a lot code being hidden away (LTO with a library is still not that common)).  
  
For `rational_adaptor`, would there be a strong drawback to storing a `number` instead of the raw integer backend? It would require telling the backend if we want expression templates, but that doesn't seem too onerous. And if all uses go through `num()` to access the backend, it probably doesn't change the code much or affect compile time significantly.  
As a hack, it should also be possible to write a backend that wraps a `number` and plug that into the adaptor...  
  
A completely different strategy would be to have `numerator` return an expression (referencing the rational), just like `a+b` returns an expression, so that hopefully the uses of the numerator would be able to use it without copying. I am not familiar enough with this package to guess how much work this would entail.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-12-04 12:27:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-986019410  

I'm leaning towards agreeing with you on this - but it will require a fair bit of refactoring - so please bare with me!

---

## Comment 5

> Username: afabri  
> Created at: 2023-02-06 12:03:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-1418972523  

Did this question get any further thought?  Could that be an interesting Google Summer of Code topic co-mentored by the CGAL project and you @jzmaddock ?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-02-08 19:29:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-1423132215  

Sorry haven't had a chance yet, a joint SOC project isn't a bad idea, but for this change the student would need to spend a lot of time understanding our internals, for what is actually a relative small amount of coding.  
  
Question, do you guys need to be able to write:  
  
```  
numerator(x) = 2;  
```  
  
If not, then expression templates would work and avoid the type-punning.  However, given that the type punning issue only effects `mpq_rational` we could just hold our noses and pun away ;)  I somewhat worry about painting ourselves into a corner when dealing with whatever future types may come along though.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2023-02-10 17:11:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/377#issuecomment-1426102405  

The referenced PR addresses this.  Note that there are some rational types where numerator/denominator still return by value, this is to support for example `debug_adaptor` where the returned type is unrelated to the stored type and adds additional state.  @afabri is this good enough to fix things at your end?
