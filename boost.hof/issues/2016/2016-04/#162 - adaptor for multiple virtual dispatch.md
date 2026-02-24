# #162 - [idea] adaptor for multiple virtual dispatch [Open]

> Username: badair  
> Created at: 2016-04-19 21:54:07 UTC  
> Updated at: 2016-04-27 05:13:38 UTC  
> Url: https://github.com/boostorg/hof/issues/162  

I know you've got a lot on your plate right now, but I just wanted to run this idea by you. I think Fit is the perfect library candidate to offer a generic, maintainable solution to the multiple dispatch problem.  
  
[This article](http://eli.thegreenplace.net/2016/a-polyglots-guide-to-multiple-dispatch/) on the topic was recently posted on Hacker News, and is an excellent overview of the problem (I was previously unfamiliar).  
  
I haven't written any code yet, but I think it an adaptor template could be made.  
  
The template would accept the following types:  
- `F` - a user-defined function object class with `operator()` overloads to handle dispatch cases  
- `B` - a base type pointer/reference  
- `D...` - a variadic parameter list of derived type candidates (pointers/references)  
  
The adaptor constructor would accept an `F` object, and keep it around as a data member (or something logically equivalent to that - you might have a better way of doing this with your magical macros).  
  
The adaptor would supply an `operator()` expecting a number of `B` arguments, which then would be `dynamic_cast`-ed to each type in the `D...` list. The `F` instance would be invoked once all arguments had been `dynamic_cast`-ed successfully, passing the successfully casted pointers as arguments.  
  
Does that make sense?  
  
If you think this is something that belongs in Fit, I could try to come up with an implementation at a later point in time.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-04-19 23:05:32 UTC  
> Updated at: 2016-04-19 23:05:48 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212161843  

Actually, I would like to implement an adaptor to aid multivisitation. One approach is this [here](http://mpark.github.io/programming/2015/07/07/variant-visitation/) which is interesting. I envision a `matrix` decorator that could be called like `matrix(n1, n2, n3)(f)(i1, i2, i3)`, which is useful to translate runtime indices to compile-time indices. So the index of a variant or a tuple can be accessed at runtime.  
  
However, I don't think this works well for general visiting pattern. So it might be nice to have another adaptor, like `visit_all(f, vs....)(xs...)` where it calls the visitor on each `x` and calls `f`. Perhaps, there is a better name, and maybe both of these could be generalized to the same adaptor. I am not sure, I would need to think about it more.  
  
> The adaptor would supply an operator() expecting a number of B arguments, which then would be dynamic_cast-ed to each type in the D... list. The F instance would be invoked once all arguments had been dynamic_cast-ed successfully, passing the successfully casted pointers as arguments.  
  
Essentially, you would do a fold a over the cross product of the derived classes. I don't provide a mechanism for that currently. I am not sure exactly how that looks, but seems to be an interesting problem. I would prefer to have something more generalized that the user could compose with `dynamic_cast` to achieve the result, as I think the components to it would be useful for other problems as well.

---

## Comment 2

> Username: viboes  
> Created at: 2016-04-19 23:24:24 UTC  
> Updated at: 2016-04-19 23:31:29 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212169595  

Hi, in P0050R0 I suggest a `select` function applied to `any` and taking a variadic list of types to inspect. `select<T1, T2, T3>(a)` behaves like a nullable variant, `variant<none, T1, T2, T3>`. Now we can apply multiple visitation on `any` as well. The same approach can be applied to a polymorphic type, but instead of using `any_cast` we use `dynamic_cast`. I could add it to the next revision with names `any_select`/`dynamic_select`.  
  
Note that the match is done sequentially :(, so the order of the selected types would be important with using dynamic selection.   
  
Would this respond to your needs?

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-04-19 23:53:18 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212175926  

> Hi, in P0050R0 I suggest a select function applied to any and taking a variadic list of types to inspect. select<T1, T2, T3>(a) behaves like a nullable variant, variant<none, T1, T2, T3>. Now we can apply multiple visitation on any as well. The same approach can be applied to a polymorphic type, but instead of using any_cast we use dynamic_cast.  
  
That is interesting. For `dynamic_cast`, the user might need to order them, or there would need to be a mechanism to know that it is the most derived. I wonder if it could be generalized such that it could be written `visit_cast<T1, T2, T3>(any_cast, a)` or `visit_cast<T1, T2, T3>(dyn_cast, a)`.  
  
> Would this respond to your needs?  
  
Beyond that, I think it would be useful to have a utility that could turn single visitors into multivisitation. Of course, I think it might be as simple as:  
  
``` cpp  
FIT_STATIC_FUNCTION(visit_all) = compose(capture(flip(apply))(compress), partial);  
```  
  
Which then the user would write `visit_all(f)(vs...)` or `visit_all(f)(select<T1, T2, T3>(a), select<T1, T2, T3>(b))`. I think `visit_all` is useful, but I do wonder if there is a better name.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-04-19 23:59:40 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212178183  

I can't help but think that this is some form of lifted function application on variants. Basically a generalized `ap` to many arguments. Perhaps this is an interesting point of view?

---

## Comment 5

> Username: pfultz2  
> Created at: 2016-04-20 14:01:47 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212438317  

> FIT_STATIC_FUNCTION(visit_all) = compose(capture(flip(apply))(compress), partial);  
  
So that won't work since the callback must always return a fixed type. I will have to think about that more.

---

## Comment 6

> Username: pfultz2  
> Created at: 2016-04-20 14:10:02 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212441023  

> I can't help but think that this is some form of lifted function application on variants. Basically a generalized ap to many arguments. Perhaps this is an interesting point of view?  
  
That is an interesting way of looking at it. This is like an applicative on visitors(not just variants). Then the `pure`(or `lift`) function could be written like this:  
  
``` cpp  
FIT_STATIC_FUNCTION(pure) = partial(flip(apply));  
```  
  
Which would wrap a single value into a visitor.

---

## Comment 7

> Username: viboes  
> Created at: 2016-04-20 21:05:46 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212605730  

What P0050R0 proposes is exactly a simple variadic pattern matching function. For sum types parameters there are as many types as alternatives. Non-sum types are considered as degraded sum-types with a single alternative.  
  
I don't follow the the applicative on visitors. Could you elaborate?

---

## Comment 8

> Username: ldionne  
> Created at: 2016-04-20 22:12:28 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-212632188  

For simplicity, let's focus on a single variant (and a function that takes a single argument). As you know, a `variant<T, _>` can be seen as an Applicative functor (it's like Haskell's `Either`). Furthermore, a `variant<T1, T2, ..., TN>` is equivalent to a `variant<T1, variant<T2, ..., TN>>`, so we can consider variants of two elements only (i.e. `variant<T, _>`) without loss of generality. Now, what we're trying to do is take a function with a set of signatures `{Ti} -> R` (for some family of `Ti`s) and make it into a function with signature `variant<T1, ..., Ti> -> R`. So far, nothing new, this is just the same as normal visitation on a variant. If we consider only the case of a variant with two types (which we can do, WLOG), that brings us down to taking a function with signature `{T1, T2} -> R` to a function `variant<T1, T2> -> R`.  
  
A function that does this would have signature `visit :: ({T1, T2} -> R) -> (variant<T1, T2> -> R)`, which looks a bit like Haskell's `liftM`, whose signature is `liftM :: Monad m => (a1 -> r) -> m a1 -> m r` (if you take the monad, `m`, to be `variant<T1, _>`). The difference is that the return type of `liftM` is lifted, while our isn't, and that my notation `{T1, T2}` is hand-wavy, because we'd have to agree on a mathematical definition of overload sets (does such a definition exist?).  
  
Now, my intuition was that a `visit` function, generalized to 2 arguments, would be a bit like Haskell's `liftM2 :: Monad m => (a1 -> a2 -> r) -> m a1 -> m a2 -> m r`. And so on and so forth for functions with more arguments. Perhaps this intuition can lead us somewhere interesting as a tought experiment, perhaps it can't. Perhaps it can be useful as a design direction, perhaps it can't. I just wanted to point it out, in case it could lead somewhere.

---

## Comment 9

> Username: pfultz2  
> Created at: 2016-04-21 17:30:23 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-213028744  

I am thinking of generalizing it over the visitors themselves rather than just a `variant`. So instead of `variant<Ts...>` it would be `({Ts...} -> R) -> R`. So for an applicative we would have something like:  
  
```  
lift :: T -> ((T -> R) -> R)    
ap :: ((({Ts...} -> {Us...}) -> R) -> R) -> (({Ts...} -> R) -> R) -> (({Us...} -> R) -> R)  
```  
  
Also `bind` would be this:  
  
```  
bind :: (({Ts...} -> R) -> R) -> ({Ts...} -> (({Us...} -> R) -> R)) -> (({Us...} -> R) -> R)  
```  
  
Then something like a variadic `liftM` could be implemented which would be the multi-visitor essentially with the result wrapped in a visitor.

---

## Comment 10

> Username: pfultz2  
> Created at: 2016-04-21 18:33:39 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-213056524  

Ok, so I haven't tested any code yet, but I was thinking something like this:  
  
``` cpp  
// Turn a single value into a visitor  
auto make_visitor = partial(flip(apply));  
  
// Transform the value the visitor passes to the callback  
auto transform = [](auto v, auto f)  
{  
    return compose(v, capture(f)(flow));  
};  
  
// Flatten a visitor of visitors to a single visitor  
auto join = capture(make_visitor)(flow);  
  
auto bind = compose(join, transform);  
  
auto binary_visitor = [](auto f, auto v1, auto v2)  
{  
    return bind(v1, [&](auto x)  
    {  
        return bind(v2, compose(make_visitor, capture(x)(f)));  
    })(identity);  
};  
```  
  
Then to support general multi-visitation then some kind of fold could be used to expand it to varidiac parameters.

---

## Comment 11

> Username: viboes  
> Created at: 2016-04-21 22:47:29 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-213149144  

I believe that pattern matching is a more primitive concept than `functors`, `applicatives`, `monads` or whatever and that PM shouldn't be defined in function of any of them, but the opposite.  
  
@ldionne Note that PM on `Either<L,R>` needs a visitor that match either `L` or `R`.  This has nothing to be with `liftM` Neither the function, neither the result.  
  
In mathematics we don't need names, so we would define an overload set as a set of functions and applying an overload set to a sum type is just what we want to define, PM on the alternatives.  
  
Maybe you have found something that I'm unable to see.

---

## Comment 12

> Username: ldionne  
> Created at: 2016-04-22 15:14:54 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-213468645  

> Maybe you have found something that I'm unable to see.  
  
Probably not. I'm also unsure where my "intuition" brings us, and whether there's actually anything to take away from it.

---

## Comment 13

> Username: pfultz2  
> Created at: 2016-04-27 05:08:08 UTC  
> Updated at: 2016-04-27 05:13:38 UTC  
> Url: https://github.com/boostorg/hof/issues/162#issuecomment-214971129  

Looking at this more, I don't think implementing visitors using a monad gains anything. Ultimately, its just function composition within composition(its like `.(.).` in haskell, I believe). Writing this in terms of the Fit library would look like this:  
  
``` cpp  
FIT_STATIC_FUNCTION(binary_compose) = partial(limit_c<2>(compose));  
FIT_STATIC_FUNCTION(visit_compose) = by(binary_compose, capture(apply)(compose));  
FIT_STATIC_FUNCTION(zip_visit) = compose(capture(partial)(flow), visit_compose);  
```  
  
Then you would call this with your visit functions like this:  
  
``` cpp  
zip_visit(visit(x), visit(y))([](auto x, auto y) { ... });  
```  
  
I haven't tested this yet, and I know it won't work in its current form, because the `binary_compose` function needs to handle the `result_type`.  
  
Also, being based on function composition, it is associative as well so:  
  
``` cpp  
zip_visit(zip_visit(xs...), zip_visit(ys...)) == zip_visit(xs..., ys...)  
```  
  
I wonder if there is better name for this. I don't like the name `zip_visit`.
