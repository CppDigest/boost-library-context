# #109 - [Foldable] Why `Foldable` `sum` function need to be called using `sum<>`? [Closed]

> Username: viboes  
> Created at: 2015-06-13 14:33:48 UTC  
> Updated at: 2015-06-18 20:57:24 UTC  
> Closed at: 2015-06-14 21:34:52 UTC  
> Url: https://github.com/boostorg/hana/issues/109  



---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 21:16:23 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-111753048  

First, the rationale for why `sum` needs the Monoid to be specified is at the documentation for `sum`, [here](http://ldionne.com/hana/structboost_1_1hana_1_1Foldable.html#ae99a05a203907d1381a00d9e05425ab7). I just updated this rationale as follows:  
  
> ## Why must we sometimes specify the `Monoid` by using `sum<M>`?  
>   
> This is because sequences like Tuple are not parameterized data  
> types (by design). Hence, we do not know what kind of objects are  
> in the sequence, so we can't know a `0` value of which type should  
> be returned when the sequence is empty. Therefore, the type of the  
> `0` to return in the empty case must be specified explicitly. Other  
> foldable structures like Ranges will ignore the suggested Monoid  
> because they know the data type of the objects they contain. This  
> inconsistent behavior is a limitation of the current design of data  
> types, and work is being done to resolve it.  
  
Then, the reason why it must be called as `sum<>(xs)` rather than `sum(xs)` is because it is a function object, not an overloaded function, so the annoying trailing `<>` can't be omitted. However, the Monoid has to be specified most of the time, so you should not end up writing `sum<>` a lot. The fact that you must specify the Monoid is a known problem, however.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 23:13:33 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-111883357  

In Haskell we have   
  
```  
sum :: (Foldable t, Num a) => t a -> a  
Source  
  
The sum function computes the sum of the numbers of a structure.  
```  
  
Your sum function been more general needs the additional parameter, but why would we like to be so general? Do you have a use case?  
  
IMHO, this function will be used with homogeneous Foldables more often than with heterogeneous ones.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-16 17:20:07 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112503046  

Regardless of homogeneous vs heterogeneous: which Monoid should it use by default? `IntegralConstant<int>`? `IntegralConstant<long>`? Could be anything, couldn't it?

---

## Comment 4

> Username: viboes  
> Created at: 2015-06-17 00:46:24 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112614873  

I missed this part  
  
"The initial state for folding is the identity of the `Monoid`."  
  
You are right that we need to give it as parameter in case the Foldable is an empty structure. Otherwise I don't see the need.   
  
We could have `sum<M>(xs)` that works even when `xs` can be empty and `sum1(x:xs)` that works only when `x:xs` has not an empty structure. I'm not requesting that.  
  
You say that you need to use `sum<>` because `sum` is a function object. I was wondering if a generic library such as Hana, shouldn't have both function objects and functions. Having a function, it can try to deduce the `Monoid` and default to `IntegralConstant<int>` when it can not deduce it. I'm not requesting Hana to have function objects and functions, I just want to know what do you think.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-17 03:37:41 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112641632  

> You say that you need to use sum<> because sum is a function object. I was wondering if a generic library such as Hana, shouldn't have both function objects and functions. Having a function, it can try to deduce the Monoid and default to IntegralConstant<int> when it can not deduce it. I'm not requesting Hana to have function objects and functions, I just want to know what do you think.  
  
Of course, a function would be better in this case since we could just use `sum(xs)` instead of `sum<>(xs)`. However, the problem with overloaded functions is that you can't pass them to higher-order algorithms. I don't think `sum` will be frequently used as an argument to higher order algorithms, and hence it would probably still cover 90% of the cases if we provided a normal function, and the syntax would be better than `sum<>`. However, for the 10% of case where it wouldn't work as expected (because _everything_ else in Hana is a function object), I think it would be surprising. I'm not sure really. Perhaps it would be more pragmatic to just use a function for `sum` and `product`, and to document the fact that they are not function objects.

---

## Comment 6

> Username: viboes  
> Created at: 2015-06-17 17:56:27 UTC  
> Updated at: 2015-06-17 17:56:48 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112892579  

My question was what do you think about having both?

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-17 18:14:43 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112901216  

What names would you use? One could be `sum`, but what would be the other? I'm open to suggestions.

---

## Comment 8

> Username: ldionne  
> Created at: 2015-06-17 18:15:57 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112901616  

Note that we could also change the name completely to something else. In Haskell, this is the `fold` function (obviously we can't name it `fold`, but you get the point).

---

## Comment 9

> Username: viboes  
> Created at: 2015-06-17 21:03:30 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-112949177  

If we had both free functions and function objects, I would name the later `hof::sum`. `hof` for High-Order-Function. A prefix could work also, `hof_sum`.   
  
 `sum_f` is a shorter name that follows the existing `_t`for alias types, `_v` for variables.

---

## Comment 10

> Username: ldionne  
> Created at: 2015-06-18 20:57:24 UTC  
> Url: https://github.com/boostorg/hana/issues/109#issuecomment-113287864  

Almost everything in Hana is a function object. Does that mean almost everything should be in the `hof::` namespace (or prefixed/suffixed with something)? Also, it must be clear that `hof::sum`, or whatever we would call it, would actually _fail_ at compile-time when it is sent an empty sequence. Is this desirable? At this point, I'm actually considering removing `sum` and `product` altogether, since they are just specific folds (no crucial pieces) and they bring their lot of problems.
