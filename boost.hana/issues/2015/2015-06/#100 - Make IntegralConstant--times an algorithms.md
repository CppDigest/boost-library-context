# #100 - Make IntegralConstant::times an algorithms [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:41:36 UTC  
> Updated at: 2015-09-09 23:37:02 UTC  
> Closed at: 2015-09-09 23:37:02 UTC  
> Url: https://github.com/boostorg/hana/issues/100  

I like a lot the literal constants.   
I like less the provided times() extension as IMO it is not needed.  
I would prefer to see these extensions as algorithms.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 14:06:22 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111712592  

From my experience, the `.times` extension is terribly useful. See for example [this](https://github.com/ldionne/Units-BLAS/commit/7b1f5362a856bef8222dd5e326b98f4f3c1bffe9#diff-8534909c15d1ed46f175067adfa88351R44) commit, which uses it a lot.  
  
Also, like I said in #65, I don't think the complexity of adding a new algorithm is worth it:  
What kind of structure do you need in order to implement `times`? At a glance, you would need something like an `Enumerable` with a lower bound, so that you can `pred(n)` until `n == lowerbound`. We have `Enumerable`, but there's no lower bound. Hence, we would need to introduce a new concept for that, and I think the added complexity is not worth it.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-13 14:07:01 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111712613  

If you have a way of implementing the algorithm without introducing a new concept, I'm willing to reopen and reconsider this, by the way.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-13 15:31:37 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111720133  

I was thinking in applying it to `std::integral_constant`. Wouldn't this be possible?

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-13 21:59:55 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111755294  

Currently, no. But why not use Hana's `integral_constant` then?

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-06-13 22:05:50 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111755479  

Ideally, it should apply to any IntegralConstant.

---

## Comment 6

> Username: ldionne  
> Created at: 2015-06-13 22:27:00 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111757194  

It does. `IntegralConstant` is _not_ a concept; it only denotes Hana's integral constants. The concept is `Constant`, and it is more general than `IntegralConstant`; see [here](http://ldionne.com/hana/structboost_1_1hana_1_1Constant.html).

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-13 22:28:03 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111757537  

What function name would you want if it were to be a free function? I think this looks a bit clunky:  
  
``` c++  
times(int_<10>, []{ std::cout << "foo" << std::endl; });  
```

---

## Comment 8

> Username: pfultz2  
> Created at: 2015-06-13 22:42:50 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111757970  

> It does. IntegralConstant is not a concept; it only denotes Hana's integral constants.   
  
This is very confusing, because it has always been a concept.   
  
> The concept is Constant, and it is more general than IntegralConstant;  
  
Yes, but why not have `IntegralConstant` be a refinement of `Constant` when the `value_type` is an integral(ie `std::is_integral` is true).  
  
> What function name would you want if it were to be a free function?  
  
Why wouldn't it be the same?  
  
> I think this looks a bit clunky:  
  
How so?

---

## Comment 9

> Username: ldionne  
> Created at: 2015-06-13 22:54:45 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111758396  

> > It does. IntegralConstant is not a concept; it only denotes Hana's integral constants.  
>   
> This is very confusing, because it has always been a concept.  
  
You're right, I did not realize that.  
  
> > The concept is Constant, and it is more general than IntegralConstant;  
>   
> Yes, but why not have IntegralConstant be a refinement of Constant when the value_type is an integral(ie std::is_integral is true).  
  
That makes sense. I'd need a name for Hana's IntegralConstants, though.  
  
> > What function name would you want if it were to be a free function?  
>   
> Why wouldn't it be the same?  
>   
> > I think this looks a bit clunky:  
>   
> How so?  
  
Well, I just think `times(int_<10>, f)` looks a bit ugly. It could be mistaken for multiplication, too. Something like `unroll(int_<10>, f)` might be better suited?

---

## Comment 10

> Username: pfultz2  
> Created at: 2015-06-13 23:05:28 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111759402  

> Well, I just think times(int_<10>, f) looks a bit ugly. It could be mistaken for multiplication, too. Something like unroll(int_<10>, f) might be better suited?  
  
Or perhaps `repeat`? Or is that already being used?

---

## Comment 11

> Username: pfultz2  
> Created at: 2015-06-13 23:07:07 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111759454  

> That makes sense. I'd need a name for Hana's IntegralConstants, though.  
  
Why not `integral_constant`?

---

## Comment 12

> Username: ldionne  
> Created at: 2015-06-14 12:39:30 UTC  
> Url: https://github.com/boostorg/hana/issues/100#issuecomment-111822567  

> Or perhaps repeat? Or is that already being used?  
  
`repeat` is already being used, but that does not mean I can't bump it to something else. `repeat` is an obviously good candidate for a free version of `.times`, but we'd have to find something else for the current `repeat`, which is used to create a sequence containing `n` copies of some value.  
  
> Why not integral_constant?  
  
Well first, that would clash with the other names used for tags, which are capitalized (I'm still writing the reply to your message [here](http://article.gmane.org/gmane.comp.lib.boost.devel/261160)). But most importantly, `integral_constant` is [this](http://ldionne.com/hana/structboost_1_1hana_1_1IntegralConstant.html#a3548ddeed18cbfe945b7de9cb65f873b). Though I could also use `integral_c`, I guess.
