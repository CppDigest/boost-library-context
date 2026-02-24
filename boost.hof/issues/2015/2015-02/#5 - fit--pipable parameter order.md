# #5 - fit::pipable parameter order [Closed]

> Username: bcachet  
> Created at: 2015-02-19 07:14:26 UTC  
> Updated at: 2015-10-07 21:19:17 UTC  
> Closed at: 2015-10-07 09:00:20 UTC  
> Url: https://github.com/boostorg/hof/issues/5  

Hello,  
  
First of all, thanks for your library. Functional Programming with C++ is possible thanks to your work.  
  
I have played with your Fit library and encounter a little problem  
  
Let say I define the following filter_ method.  
  
``` c++  
struct filter_ {  
  template<class L, class F>  
    L operator()(F f, L l) const {  
      L result;  
      for (auto& item : l)  
        if (f(item))  
          result.push_back(item);  
      return result;  
    }  
};  
```  
  
I can use it with partial  
  
``` c++  
auto filter = fit::partial(filter_());  
```  
  
In order to some filtering:  
  
``` c++  
auto odd = fit::_1 % 2;  
  
assert(filter(odd)(std::list<int>({1, 2, 3, 4, 5})) == {1, 3, 5});  
```  
  
But if I want to reuse my filter_ method with fit::pipable, I have to rewrite it just to exchange the order of the parameters:  
  
``` c++  
// do not compile because of params that are not in expected order (list first, method second)  
std::list<int>({1, 2, 3, 4, 5}) | fit::pipable(filter_())(odd);   
```  
  
Do I miss something ? Is it possible to adapt fit::pipable so that we can use it with same function object as fit::partial, fit::fuse ...  
  
Have a nice day  
  
Bertrand

---

## Comment 1

> Username: viboes  
> Created at: 2015-02-19 07:52:06 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-75010900  

I guess Fit has flip function that flips the first and second parameters. I would suggest to pipe flip filter_.

---

## Comment 2

> Username: bcachet  
> Created at: 2015-02-19 08:47:33 UTC  
> Updated at: 2015-02-19 08:53:36 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-75016095  

There is no flip function. But I should be able to write one (maybe using fit::placeholders). But question is more: why do fit::pipable do not have same parameter order than others fit::function ?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-02-19 12:46:03 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-75046153  

Fit doesn't have a `flip` method. I could add one. But either way it still wouldn't work, due to interactions between the both function adaptors. With the pipable adaptor, the function is still callable as is:  
  
``` cpp  
auto sum = pipable([](auto x, auto y) { return x + y; };  
  
// We can call it like this  
auto r1 = sum(1, 2);  
  
// Or we can call it like this  
auto r2 = 1 | sum(2);  
```  
  
To do this, pipable just checks if the base function is callable, and then pipable calls the base function.  
  
The problem comes with partial, which makes the function always callable because if the base function is not callable it returns a new function. This doesn't play nicely with pipable.  
  
I do have some specializations in partial to remove the pipable-ness of a function [here](https://github.com/pfultz2/Fit/blob/master/fit/partial.h#L230). In the past, I usually define the function as pipable and then I use partial as needed(partly due to the fact that partial can mask the true error when the function isn't called correctly).   
  
So that specialization works for this use case, but it is more of hack, and it doesn't scale well. Rather the function adaptors should recognize that it is adapting a "masked" function. And then when another mask adaptor is applied, there is a way to re-apapt the function without the mask.  
  
I will need to think about this some more.  
  
> But question is more: why do fit::pipable do not have same parameter order than others fit::function ?  
  
Because in C++, most people see these things as equivalent: `x | f(y)`, `x.f(y)`, and `f(x, y)`. Putting the piped parameter at the end would be confusing to a lot of people.

---

## Comment 4

> Username: bcachet  
> Created at: 2015-02-19 12:57:14 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-75047486  

Ok. I understand the parameter order and seems totally legit.  
  
I will declare my map/filter ... functions as pipable and will work with  
both _| operator_ and call map(list, method) even if it doesn't seem really  
familiar to me.  
As soon as you manage to find a way to use same function object for  
pipable/partial I will update my code.  
  
Thanks for your answer  
  
Bertrand  
  
On Thu, Feb 19, 2015 at 1:46 PM, Paul Fultz II notifications@github.com  
wrote:  
  
> Fit doesn't have a flip method. I could add one. But either way it still  
> wouldn't work, due to interactions between the both function adaptors. With  
> the pipable adaptor, the function is still callable as is:  
>   
> auto sum = pipable([](auto x, auto y) { return x + y; };  
> // We can call it like thisauto r1 = sum(1, 2);  
> // Or we can call it like thisauto r2 = 1 | sum(2);  
>   
> To do this, pipable just checks if the base function is callable, and then  
> pipable calls the base function.  
>   
> The problem comes with partial, which makes the function always callable  
> because if the base function is not callable it returns a new function.  
> This doesn't play nicely with pipable.  
>   
> I do have some specializations in partial to remove the pipable-ness of a  
> function here  
> https://github.com/pfultz2/Fit/blob/master/fit/partial.h#L230. In the  
> past, I usually define the function as pipable and then I use partial as  
> needed(partly due to the fact that partial can mask the true error when the  
> function isn't called correctly).  
>   
> So that specialization works for this use case, but it is more of hack,  
> and it doesn't scale well. Rather the function adaptors should recognize  
> that it is adapting a "masked" function. And then when another mask adaptor  
> is applied, there is a way to re-apapt the function without the mask.  
>   
> I will need to think about this some more.  
>   
> But question is more: why do fit::pipable do not have same parameter order  
> than others fit::function ?  
>   
> Because in C++, most people see these things as equivalent: x | f(y),  
> x.f(y), and f(x, y). Putting the piped parameter at the end would be  
> confusing to a lot of people.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/pfultz2/Fit/issues/5#issuecomment-75046153.  
  
##   
  
Bertrand  
  
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .  
☎: +41 78 664 20 88  
🏠: Route de Neuchâtel 3; 2520 La Neuveville; Switzerland

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-02-25 16:15:20 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-75989805  

Ok so I added a `flip` adaptor so you can flip the parameters. You still can't apply `pipable` to `partial` yet, but you should at least be able to do `pipable_adaptor<flip_adaptor<filter_>>`.

---

## Comment 6

> Username: bcachet  
> Created at: 2015-02-25 21:10:24 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-76059256  

Thanks for this addition.  
I will have a look to your implementation. Didn't manage to create one.

---

## Comment 7

> Username: viboes  
> Created at: 2015-10-04 12:18:33 UTC  
> Updated at: 2015-10-04 12:24:46 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-145342981  

@bcachet  I suspect I misunderstood your concern.  
  
I wonder if partial shouldn't know the signature of the function it is currying. When there are some parameter not yet bound it returns a function, if all are provided we have two options:  
A \* return a nullary function.  
B \* return the result  
  
```  
partial:: ((T..., U...)->V) T... -> (U... -> V)  
```  
  
```  
partial:: ((A B -> D) A B -> ( () -> V) // Option A  
partial:: ( A B- > D) A B. -> V // Option B  
```  
  
Does partial works like A ?  
Then partial and curry are not the same, which can be useful, of course.  
Are we interested in a curry function that works like option B?  
Can we have both partial and curry?  
  
I'm confused by   
  
``` c++  
// sum: A -> B -> C  
auto r2 = 1 | sum(2);  
```  
  
IMHO this should call `sum(2,1)`  
  
For me, if `sum` is curryable `sum(2)` should be a function having a single parameter   
  
```  
// sum(2): B -> C  
```  
  
So `1 | sum(2)` should call `sum(2,1)`.

---

## Comment 8

> Username: viboes  
> Created at: 2015-10-04 12:24:58 UTC  
> Updated at: 2015-10-04 12:25:47 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-145344240  

After reading it seems  that partial implements option B, which is great.  
  
Do we agree then that    
  
```  
partial(sum)(1)(2)) <==> sum(1,2)  
```  
  
?  
  
I don't understand yet why `2 | sum(1)` would call `sum(2,1)`.

---

## Comment 9

> Username: bcachet  
> Created at: 2015-10-04 13:12:11 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-145349331  

@viboes I agree with you saying that   
  
```  
partial(sum)(1)(2)) <==> sum(1,2)  
```  
  
Parameter order when using pipe has been explained by @pfultz2:  
  
> Because in C++, most people see these things as equivalent: `x | f(y)`, `x.f(y)`, and `f(x, y)`. Putting the piped parameter at the end would be confusing to a lot of people.  
  
So `x | f(y)` means: apply the `f(y)`to `x` which is also `x.f(y)`. Due to the _this_ parameter it becomes `f(x, y)`

---

## Comment 10

> Username: viboes  
> Created at: 2015-10-04 14:00:50 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-145351949  

Yes, but I don't agree that `f(y)` applied to `x` is equivalent to `x.f(y)`. If `x | fct` has a sens is because `fct`is pipable, and then equivalent to `fct(x)`. Here `f(y)` must be a Pipable, not `f` and  so `x | f(y)`must be equivalent to `f(y)(x)`

---

## Comment 11

> Username: bcachet  
> Created at: 2015-10-04 15:10:48 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-145356676  

I agree and that was my original feeling.  
Paul seemed to take this decision to help people coming from C++ and OOP.  
  
##   
  
Bertrand  
  
> Le 04 Oct 2015 à 16:00, Vicente J. Botet Escriba notifications@github.com a écrit :  
>   
> Yes, but I don't agree that f(y) applied to x is equivalent to x.f(y). If x | fct has a sens is because fctis pipable, and then equivalent to fct(x). Here f(y) must be a Pipable, not f and  so x | f(y)must be equivalent to f(y)(x)  
>   
> —  
> Reply to this email directly or view it on GitHub.

---

## Comment 12

> Username: pfultz2  
> Created at: 2015-10-06 20:48:24 UTC  
> Updated at: 2015-10-06 20:48:44 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-145996186  

> Yes, but I don't agree that f(y) applied to x is equivalent to x.f(y). If x | fct has a sens is because fctis pipable, and then equivalent to fct(x). Here f(y) must be a Pipable, not f and so x | f(y)must be equivalent to f(y)(x)  
  
Most C++ programmers won't see it that way. They see pipable functions as an extension method. So they see `x | f(y)` and `x.f(y)` as the same. Furthermore, range-v3 takes a similar approach with pipable functions as well.   
  
The pipe operator is not the same as function application. That is why it is separate. The behaviour you want can already be easily achieved using `partial` and `apply`:  
  
``` cpp  
auto $ = infix(flip(apply));  
auto sum = partial([](auto x, auto y) { return x+y; });  
auto i = 2 <$> sum(1);  
```  
  
So now `2 <$> sum(1)` is the same as `sum(1, 2)`

---

## Comment 13

> Username: viboes  
> Created at: 2015-10-07 17:10:52 UTC  
> Updated at: 2015-10-07 21:19:17 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-146264807  

Could you show how similar is the range-v3, any pointers?

---

## Comment 14

> Username: pfultz2  
> Created at: 2015-10-07 20:18:54 UTC  
> Url: https://github.com/boostorg/hof/issues/5#issuecomment-146316259  

Look here how [`view::transform`](https://ericniebler.github.io/range-v3/structranges_1_1v3_1_1view_1_1transform__fn.html) is defined. It has a signature of:  
  
``` cpp  
auto transform(Rng &&rng, Fun fun)  
```  
  
Where it takes the range first and then the function. Yet it is called like this: `rng | view::transform(fun)`(or `view::transform(rng, fun)` if you are not using the piped version). The same semantics is done for `fit::pipable`.
