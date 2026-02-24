# #77 - capture - more description is needed to understand the differences [Closed]

> Username: viboes  
> Created at: 2015-10-04 17:14:07 UTC  
> Updated at: 2016-03-07 00:03:57 UTC  
> Closed at: 2015-12-17 20:31:50 UTC  
> Url: https://github.com/boostorg/hof/issues/77  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 07:21:56 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-145447980  

What differences?

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 21:56:35 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-145679525  

The differences between the 3 capture functions.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 22:03:11 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-145681789  

I guess it would be better to use the descriptions from `fit::pack`.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 23:44:07 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-145699601  

Humm, what is the difference between capture and pack?

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-09 16:36:48 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-146924021  

Like this:  
  
``` cpp  
assert(pack(xs...)(f) == f(xs...));  
assert(capture(xs...)(f)(ys...) == f(xs..., ys...));  
```  
  
Those are the semantics side by side. `fit::capture` uses `fit::pack` and `fit::pack_join`, like this:  
  
``` cpp  
assert(capture(xs...)(f)(ys...) == pack_join(pack(xs...), pack(ys...))(f));  
```  
  
`fit::pack` is used by a lot of adaptors when the parameters need to be stored and invoked later.

---

## Comment 6

> Username: viboes  
> Created at: 2015-10-09 17:22:16 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-146938091  

OK I see, so if `f` has `n` parameters   
  
```  
capture(x1, ..., xn)(f)() ==   pack(x1, ..., xn)(f)  
```  
  
You need to describe the differences between the different version of `pack`.

---

## Comment 7

> Username: pfultz2  
> Created at: 2015-10-09 18:41:35 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-146957743  

> You need to describe the differences between the different version of pack.  
  
I do, already, but I can use the same description for `capture`, because I think the descriptions for `fit::pack` are better. Perhaps not. What do you think?

---

## Comment 8

> Username: viboes  
> Created at: 2015-10-09 19:08:50 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-146963432  

I know that it takes some time but I'm for the kind of description th standard use. E.g.  
  
```  
template<class... Types>  
constexpr tuple<VTypes...> make_tuple(Types&&... t);  
```  
  
Let `Ui` be `decay_t<Ti>` for each `Ti` in `Types`. Then each `Vi` in `VTypes` is `X&` if `Ui`equals `reference_wrapper<X>`, otherwise `Vi` is `Ui`.  
Returns: `tuple<VTypes...>(std::forward<Types>(t)...)`.  
  
BTW, can `pack/pack_perfect/pack_decay` be defined in function of `make_tuple/forwards_as_tuple/tie'.  
How are they related?  
  
For consistency with the standard, shouldn't `pack_join` be named `pack_cat`?

---

## Comment 9

> Username: pfultz2  
> Created at: 2015-10-09 20:32:12 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-146979670  

> I know that it takes some time but I'm for the kind of description th standard use.  
  
I will have to think about that. Right now, the type returned from `pack` is unspecified type.  
  
> BTW, can pack/pack_perfect/pack_decay be defined in function of `make_tuple/forwards_as_tuple/tie'.  
  
Well `pack_decay` is like `make_tuple`, and `pack_forward` is like `forward_as_tuple`. There is no `tie` equivalent. `pack` doesn't store any rvalue references, and is in general a good choice for good performance and avoiding dangling references. That is why it is given the shortest name.  
  
All these apply to the `capture` overloads as well. Actually, capture defers to the corresponding pack function.  
  
> For consistency with the standard, shouldn't pack_join be named pack_cat?  
  
It is not exactly the same. Plus, it sounds funny.

---

## Comment 10

> Username: pfultz2  
> Created at: 2015-10-30 23:18:27 UTC  
> Url: https://github.com/boostorg/hof/issues/77#issuecomment-152672057  

I change the description.
