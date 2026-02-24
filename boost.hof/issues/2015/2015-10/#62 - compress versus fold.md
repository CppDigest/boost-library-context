# #62 - compress versus fold [Open]

> Username: viboes  
> Created at: 2015-10-04 09:53:25 UTC  
> Updated at: 2016-03-16 18:41:49 UTC  
> Url: https://github.com/boostorg/hof/issues/62  

There are two variants of fold, left associative versus right.  
  
What about naming them `fold_left` and `fold_right`?  
  
Given an adaptor that results in a variadic function as `compress`   
  
compress:: F A -> (B.... -> A)  
  
we could define it by using a function having a tuple as parameter  
  
```  
compress(f, z)(xs...) = fold(f, z, forward_as_tuple(xs...))  
```  
  
I believe that we have already this kind of fold fuctions in Hana.  
  
So given   
  
```  
fold: F A H -> A   
```  
  
`partial(fold, F, A)` would almost behave like compress except that `partial(fold, F A)` takes a tuple as parameter and compress takes a variadic number of arguments. So we can see compress something like  
  
```  
compress(f, a) = compose(partial(fold, f, a), forward_as_tuple)  
```  
  
I'm not saying that compress is not useful, even if the name has not too much relation to what is behind the scenes. I would say that it could be a useful shortcut when the user doesn't has already a sequence or a tuple.   
  
Of course, in C++ we don't want to pay more than needed, so the implementation should be more efficient than using the right hand side.   
  
It would be fanny to have some performance measures. This is micro-benchmarking, but having the figures would help the user to understand the trade-offs.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:48:27 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-145444489  

`fit::compress` doesn't use sequences, but if you want to do fold over a sequence, you can simply do `unpack(compress(f))(seq)`.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:22:27 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-145686414  

My concern is that we have a lot of functions that work already on tuples.  
  
f :: F T TUPLE  -> T  
  
For each function like that we can propose a function  
  
vf :: F T T...  -> T  
  
and also   
  
cf :: F T  -> (T... -> T)  
  
I would like that we have just a function and that we use adaptor to have the other interfaces.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 22:52:20 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-145691382  

> My concern is that we have a lot of functions that work already on tuples.  
  
There are no functions in Fit that work on tuples except `fit::unpack`. What are you referring to?  
  
> For each function like that we can propose a function  
  
Im sorry, I don't understand your notation at all.  
  
> I would like that we have just a function and that we use adaptor to have the other interfaces.  
  
I don't understand what you are suggesting still, but if you are suggesting that Fit should have a `fold` for tuples, I think that is beyond the scope of Fit. Instead, you should look to using either Boost.Hana, or my [Hero](https://github.com/pfultz2/Hero) library(its still a WIP) which already implements [`fold`](https://github.com/pfultz2/Hero/blob/master/include/hero/fold.h) for heterogeneous sequences.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-06 00:15:12 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-145704221  

It is independently from where the function having an heterogeneous signature comes from.  
  
Do we want to have 3 variant for each one of these functions or have only one and just adaptors to move from one to the other.  
  
By  
  
```  
f :: A B -> C  
f :: A B... -> C  
  
```  
  
I mean a function with two arguments A and B and having as result a type C  
  
In   
  
```  
g :: A B... -> C  
```  
  
The type B is variadic

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-09 20:52:51 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-146983426  

> I mean a function with two arguments A and B and having as result a type C  
  
So a function like this:  
  
``` cpp  
template<class A, class B>  
C foo(A a, B b);  
```  
  
> The type B is variadic  
  
So you mean this:  
  
``` cpp  
template<class A, class... Bs>  
C foo(A a, Bs... bs);  
```  
  
So are you suggesting that `compress` have a signature like this?  
  
``` cpp  
template<class F, class... States>  
constexpr compress_adaptor<F, States...> compress(F f, States... s);  
```

---

## Comment 6

> Username: viboes  
> Created at: 2015-10-10 02:50:22 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-147026284  

Not at all. I'm saying that given a function that works on sequences, e.g. fold  
  
```  
fold :: (A,B)->A A Seq<B> -> A  
```  
  
we can always create another partial function that results in a function having  the sequence as parameter   
  
```  
partial_fold :: (A,B)->A -> A -> Seq<B> -> A   
```  
  
`partial_fold` is almost equivalent to `partial(fold)`,   
  
and that this function taking a sequence as parameter can be seen as a variadic function  
  
```  
compress :: (A,B)->A A -> B... -> A   
```  
  
`compress(f,z)` is equivalent to `unpack(partial(fold, f, z))`,   
  
After some time a user can understand the coherency of `pack`/`unpack` and `partial`. The user knows already what `fold` does. I believe that It is more difficult to make the association between `compress` and `fold` if we see only the names. I'm wondering if the name `fold` shouldn't be part of the function you name `compress` , e.g. `variadic_partial_fold`.  
  
Note also that we have fold left and fold right, should we have `compress_left` and `compress_right`?  
  
```  
compress(f,z)(xs...)  
pack(xs...) | partial(fold, f, z)  
  
pack(xs...) | partial(fold_left, f, z)  
pack(xs...) | partial(fold_right, f, z)  
```  
  
Each time I think of `fold` I think of folding something, I have already a sequence and so I would need to   
unpack compress  
  
` unpack(compress(f,z))(seq)'  
  
Do you have concrete examples where the user has `T...` and not a sequence?  
  
I'm wondering if `unpack` shouldn't take a function and possibly other arguments so that  
  
```  
unpack(f, xs...)(zs...) <=> f(xs..., pack(ys))  
```  
  
and so  
  
```  
compress(f,z) <=> unpack(fold, f, z)  
```  
  
This gives us the choice between  
  
```  
compress(f, z)(xs...)  
unpack(compress(f,z))(seq)  
```  
  
and  
  
```  
unpack(fold, f, z)(xs...)  
partial(fold, f, z)(seq)  
```  
  
So the question is, would we need `compress` if we had `fold`and the previous `unpack`? IMHO, `unpack(fold, f, z)(xs...)` is easier to learn than `compress(f, z)(xs...)` with the bonus that we have also   
  
```  
unpack(fold_left, f, z)(xs...)  
unpack(fold_right, f, z)(xs...)  
partial(fold_left, f, z)(seq)  
partial(fold_right, f, z)(seq)  
```

---

## Comment 7

> Username: pfultz2  
> Created at: 2015-10-25 23:25:57 UTC  
> Updated at: 2015-10-25 23:30:54 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-150987015  

> Do you have concrete examples where the user has T... and not a sequence?  
  
There are a lot of examples. Creating variadiac `and_` function(which is useful due to lack of fold expressions):  
  
``` cpp  
FIT_STATIC_FUNCTION(and_) = compress(_ and _);  
```  
  
Creating a varidiac `max` function:  
  
``` cpp  
FIT_STATIC_LAMBDA_FUNCTION(vmax) = compress(FIT_LIFT(std::max));  
```  
  
Furthermore, fold over a sequence would require using `compress`:  
  
``` cpp  
FIT_STATIC_LAMBDA_FUNCTION(fold) = [](auto&& sequence, auto f)  
{  
    return fit::unpack(fit::compress(f))(std::forward<decltype(sequence)>(sequence));  
};  
```  
  
> I'm wondering if unpack shouldn't take a function and possibly other arguments so that  
  
You can already do that with `fit::capture`:  
  
``` cpp  
unpack(capture(f, xs...))(zs...) <=> f(xs..., pack(ys))  
```  
  
Except, I don't understand the `pack(ys)` part. `unpack` takes multiple sequences to unpack. So you can do this:  
  
``` cpp  
assert(3 == unpack(_+_)(std::make_tuple(1), std::make_tuple(2));  
```  
  
Since it unpacks multiple sequences, `tuple_cat` can be implemented using `unpack`:  
  
``` cpp  
FIT_STATIC_FUNCTION(tuple_cat) = unpack(construct<std::tuple>());  
```  
  
Finally, this post [here](http://pfultz2.com/blog/2015/09/12/power-of-unpack/) explains in further detail how to implement basic algorithms over tuples and sequences using the Fit library. However, a heterogeneous sequence library is beyond the scope of the Fit library. I want to keep the library focused on functions and not sequences.

---

## Comment 8

> Username: viboes  
> Created at: 2016-03-01 00:21:34 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-190465200  

I agree with keeping the library focused on functions. Nevertheless, I believe that including some references to fold could help the user to understand what is behind, and that in the case of variadics this interface could even be more efficient.  
  
What if clear to you it is not always clear to others.

---

## Comment 9

> Username: viboes  
> Created at: 2016-03-05 13:51:42 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192649503  

What is wrong renaming `compress` to `fold`?

---

## Comment 10

> Username: pfultz2  
> Created at: 2016-03-05 17:25:11 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192692778  

I mainly did this to avoid confusing it with `fold` done over a sequence.

---

## Comment 11

> Username: viboes  
> Created at: 2016-03-05 21:17:15 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192739527  

Yes, but compress is doing just the same on the sequence of pack parameters.  
Does this need another name?

---

## Comment 12

> Username: pfultz2  
> Created at: 2016-03-05 22:41:38 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192754230  

Well, if later I decide as an extension to add some basic algorithms for sequences, I wouldn't be able to call it `fold`.

---

## Comment 13

> Username: ldionne  
> Created at: 2016-03-05 23:51:57 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192762045  

Is this something you plan to do?

---

## Comment 14

> Username: viboes  
> Created at: 2016-03-05 23:54:32 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192762172  

The signature will be different, isn't it?

---

## Comment 15

> Username: ldionne  
> Created at: 2016-03-05 23:55:48 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192762223  

What I'm asking is simply whether adding algorithms on sequences is something Paul has in mind.

---

## Comment 16

> Username: pfultz2  
> Created at: 2016-03-05 23:59:10 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192762499  

I am not planning on it currently, however, I don't want to close the door to the possibility.

---

## Comment 17

> Username: ldionne  
> Created at: 2016-03-06 00:05:05 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-192763176  

IMHO, if you're not realistically planning on it, then `fold` is a much better name than `compress`. And if you were planning on it realistically, I would argue that keeping the scope of the library small (function objects only) is better.

---

## Comment 18

> Username: pfultz2  
> Created at: 2016-03-15 20:52:57 UTC  
> Updated at: 2016-03-15 20:53:10 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197014757  

If its called `fold` then fold for a sequence would look like this:  
  
``` cpp  
FIT_STATIC_LAMBDA_FUNCTION(fold) = [](auto&& sequence, auto f)  
{  
    return fit::unpack(fit::fold(f))(std::forward<decltype(sequence)>(sequence));  
};  
```  
  
I think the double fold looks a little strange. I wonder if theres a better name. Also, `compress` could be implemented in terms of `compose`:  
  
``` cpp  
auto compress = [](auto f)   
{  
    return by(partial(f), compose);  
}  
```  
  
However, we can't implement a fix point combinator using `fit::reverse_compress`. At least, I don't see how.

---

## Comment 19

> Username: ldionne  
> Created at: 2016-03-15 21:03:58 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197018251  

> If its called fold then fold for a sequence would look like this:  
> [...]  
> I think the double fold looks a little strange.  
  
I don't think it looks strange, actually I think it highlights the fact that `fit::compress` is really a fold. Of course this is "just" naming, but I think it would be wrong to introduce another completely unknown term for something that's effectively a fold.

---

## Comment 20

> Username: viboes  
> Created at: 2016-03-15 22:28:09 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197052792  

The fold for a sequence should has the signature : function, sequence, not sequence, function.  
  
One of the major problems with the library is that the arguments are not constrained.  
The behavior of `compress(f)`/`fold(f)` should depend on the type of the argument `x`  
  
```  
assert(compress(f)(x) == x);  
assert(compress(f)(x, y, xs...) == compress(f)(f(x, y), xs...));  
```  
  
if `x` is a sequence, it should be defined as a fold on a sequence.   
  
The fact that your compress has been curried, forces the decision of the behavior to be done only when the parameter are available.  
  
Note that I'm not saying that your library must define fold for sequences.

---

## Comment 21

> Username: pfultz2  
> Created at: 2016-03-15 22:41:07 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197055937  

> The fold for a sequence should has the signature : function, sequence, not sequence, function.  
  
In C++ its always defined with the sequence first. Even Hana defines it that way.  
  
> if x is a sequence, it should be defined as a fold on a sequence.   
  
Auto unpacking sequences can have unexpected surprises especially in generic code, so I would like to avoid such cleverness.  
  
> The fact that your compress has been curried, forces the decision of the behavior to be done only when the parameter are available.  
  
I don't understand. The uncurried version would make the same decision when the parameter is available.

---

## Comment 22

> Username: pfultz2  
> Created at: 2016-03-15 22:42:34 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197056429  

> I don't think it looks strange, actually I think it highlights the fact that fit::compress is really a fold.   
  
However, it will never be `Foldable` in the Hana sense.

---

## Comment 23

> Username: viboes  
> Created at: 2016-03-15 22:56:23 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197059363  

Yes, I know that a lot of libraries define fold as sequence, function. I believe however this is subject to discussion. As your library show, when the parameter of the function are the sequence we must place the function before. Of course Haskell place the function before :)  
  
It is not auto unpacking, it is about the type of the function. If the function takes the sequence as argument, fold should not unpack, but if the function takes the element of a sequence then is must unpack.    
  
The uncurried version could make the decision on the definition of the fold function. The curried one must transfer this responsibility to the result of fold.  
  
You are right that parameter packs, as they are not a type can not be taken for a  `Hana::Foldable`. However we can extend the `Foldable` concept to take care of parameter packs.   
  
At the end we are in C++.

---

## Comment 24

> Username: pfultz2  
> Created at: 2016-03-15 23:28:13 UTC  
> Updated at: 2016-03-15 23:29:58 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197067498  

> It is not auto unpacking, it is about the type of the function. If the function takes the sequence as argument, fold should not unpack, but if the function takes the element of a sequence then is must unpack.   
  
That seems backwards. Why would it not unpack if it were a sequence? The fold becomes pointless without unpacking the sequence first. I thought you would want to fold over a sequence, if its given a sequence. Something like this:  
  
``` cpp  
auto fold = [](auto f, auto... xs)  
{  
    conditional(  
        if_(and_(is_unpackable<decltype(xs)>()...)(compose(unpack, compress)),  
        compress  
    )(xs...);  
};  
```  
  
So then you could write `fold(_+_, 1, 2, 3)` and it will give you 6, and you could write `fold(_+_, make_tuple(1, 2, 3))` and it will give you six as well. However, I think this is a bad, and can lead to a lot of surprises.   
  
> You are right that parameter packs, as they are not a type can not be taken for a Hana::Foldable. However we can extend the Foldable concept to take care of parameter packs.   
  
No it can't be extended. There is no way to distinguish a variadiac pack of one from a sequence. That is, which is a `std::tuple<int>`? Is it a variadiac pack of just one tuple? or is it a sequence of one integer? There is no way to know for sure what the semantics are unless you have the user explicity add what it is(such as `fold<sequence>(f, std::tuple<int>(5))` or `fold<variadiac>(f, std::tuple<int>(5))`). And that is beyond the scope what the Fit library does.  
  
I would rather focus on the semantics that compress provides and not confuse it with other concepts such as `Foldable`. However, I do fear that naming it `fold` could lead users to that confusion(as this discussion seems to demonstrate).  
  
Perhaps, a name like `reduce` might be better, as many are familiar with the concept of `reduce` from other languages. Although it is generally associated with parallel algorithms in C++. I am not quite sure it would cause quite so much confusion as `fold` would, since Fit is far from being a parallel algorithms library.

---

## Comment 25

> Username: ldionne  
> Created at: 2016-03-16 14:22:12 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197352417  

If you call it `accumulate`, everybody would understand, no? @tzlaine We're debating on whether `fit::compress` should be called `fit::compress`, `fit::fold` or something else. Do you have an opinion? I'm asking because your super duper pragmatism in terms of naming has been helpful to me in the past.  
  
@viboes By the way, regarding the order of parameters:  
1. C++ always puts the function before, and consistency matters.  
2. The lack of automatic currying in C++ makes it not-so-useful to put the function first, since you can't do e.g. `foldl (+)` as you can in Haskell.  
3. Putting the function last makes it prettier when defining lambdas. Consider  
     
   ``` c++  
     fold(sequence, state, [](auto x, auto y) {  
        // something  
     });  
   ```  
     
   vs  
     
   ``` c++  
     fold([](auto x, auto y) {  
         // something  
     }, state, sequence);  
   ```  
     
   I know which one I prefer.

---

## Comment 26

> Username: tzlaine  
> Created at: 2016-03-16 15:38:43 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197387646  

On Wed, Mar 16, 2016 at 9:22 AM, Louis Dionne notifications@github.com  
wrote:  
  
> If you call it accumulate, everybody would understand, no?  
>   
> Yes, but I think in the C++ community people would expect accumulate always  
> to be numeric, and this is more generally useful.  I think fold() and  
> reverse_fold() tell everything you need to know.  compress() will _always_  
> require the uninitiated to immediately consult the documentation.  
>   
> @viboes https://github.com/viboes By the way, regarding the order of  
> parameters:  
> 1. C++ always puts the function before, and consistency matters.  
> 2. The lack of automatic currying in C++ makes it not-so-useful to put  
>    the function first, since you can't do e.g. foldl (+) as you can in  
>    Haskell.  
>    3.  
>      
>    Putting the function last makes it prettier when defining lambdas.  
>    Consider  
>      
>     fold(sequence, state, [](auto x, auto y) {  
>        // something  
>     });  
>      
>    vs  
>      
>     fold([](auto x, auto y) {  
>         // something  
>     }, state, sequence);  
>      
>    I know which one I prefer.  
>   
> +1  
  
Zach

---

## Comment 27

> Username: pfultz2  
> Created at: 2016-03-16 17:16:36 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197438674  

> Yes, but I think in the C++ community people would expect accumulate always to be numeric, and this is more generally useful. I think fold() and reverse_fold() tell everything you need to know.   
  
Except that it could be a false cognate, as `fit::fold` will never be `Foldable`.  
  
> compress() will _always_ require the uninitiated to immediately consult the documentation.  
  
Perhaps there is another name that is not fold, that could be immediately obvious, as well. The alternative names listed for fold on wikipedia are:  
- reduce  
- accumulate  
- aggregate  
- compress  
- inject

---

## Comment 28

> Username: tzlaine  
> Created at: 2016-03-16 18:02:13 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197460126  

> Except that it could be a false cognate, as fit::fold will never be Foldable.  
  
You never define Foldable in your library.  As a user, why do I care that hana::Foldable exists and that it is different?  At most this should warrant a note that indicates this in the docs.

---

## Comment 29

> Username: pfultz2  
> Created at: 2016-03-16 18:40:10 UTC  
> Updated at: 2016-03-16 18:41:49 UTC  
> Url: https://github.com/boostorg/hof/issues/62#issuecomment-197479444  

> You never define Foldable in your library. As a user, why do I care that hana::Foldable exists and that it is different?  
  
Because a user may use their understanding of hana::Foldable to use fit::fold, and there maybe a disconnect.  
  
>  At most this should warrant a note that indicates this in the docs.  
  
Thats probably the best option. Hopefully, the user will look at the docs.
