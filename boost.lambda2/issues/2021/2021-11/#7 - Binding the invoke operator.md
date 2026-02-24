# #7 - Binding the invoke operator [Closed]

> Username: nwotero  
> Created at: 2021-11-29 22:04:00 UTC  
> Updated at: 2021-11-30 19:42:02 UTC  
> Closed at: 2021-11-30 16:53:14 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7  

The `lambda2_arg<I>` objects overload `operator()` and `operator[]`, `operator[]` does what I expect and returns a bound function object calling the subscript operator, however `operator()` just selects an object from a tuple instead of returning a bound function object calling the invoke operator. What is the rationale behind this? And is it possible to bind the invoke operator with lambda2?  
  
Something along the lines of:  
```  
std::vector<std::function<bool()> fns = { ... };  
std::ranges::any_of(fns, _1());  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-30 16:45:01 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7#issuecomment-982817360  

`_1(x, y)` returns `x` for the same reason `(_1 + _1)(x, y)` returns `x + x` and `(_1 + 0)(x, y)` returns `x + 0` - `_1` is a lambda expression like the rest. It's not possible to determine in general whether `L(x, y)` should evaluate the lambda expression or create a new lambda expression that applies `(x, y)` to `L`.  
  
There's unfortunately no good way to get the intended `_1()` behavior. Boost.Bind supplied a function object `boost::apply` for this purpose, so that one could write `boost::bind( boost::apply<bool>(), _1 )`. But that's so verbose that there's no point to it anymore; you could just as well use `[](auto&& fn){ return fn(); }`.

---

## Comment 2

> Username: nwotero  
> Created at: 2021-11-30 16:53:14 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7#issuecomment-982824971  

Ah I understand, I missed that the placeholders themselves are meant to function as lambdas as well. Thank you for the clarification.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-11-30 18:50:04 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7#issuecomment-982919726  

The `->*` operator that was added in 1.78 allows one to achieve the equivalent of `_1()` as `_1->*invoke()` (https://godbolt.org/z/rTqj9fhKn) but that's probably too much of a contortion.

---

## Comment 4

> Username: nwotero  
> Created at: 2021-11-30 19:12:21 UTC  
> Updated at: 2021-11-30 19:14:26 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7#issuecomment-982937221  

It would be interesting if that invoke could be a first class member function of the lambda2_arg class, so that it wouldn't conflict with `operator()` but it would still be clear what the intention was. I wouldn't mind writing ` _1.invoke(_2, _3)` in my code. I understand if that goes against the purity of only having operator overloads though.  
  
Edit: Having the placeholders as arguments might be a bit ambitious, and isn't covered by the example you linked, but even `_1.invoke(5)` would be great.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-11-30 19:21:21 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7#issuecomment-982944163  

`_1.invoke` will only work on the placeholders though, and not on lambda expressions such as `*_1`.  
  
Yeah, `_1->*invoke(_2)` doesn't work with the implementation I posted. It's probably possible to make that work properly with some more effort. :-)

---

## Comment 6

> Username: nwotero  
> Created at: 2021-11-30 19:26:23 UTC  
> Updated at: 2021-11-30 19:42:02 UTC  
> Url: https://github.com/boostorg/lambda2/issues/7#issuecomment-982948056  

Do you think that is an unreasonable limitation? In my opinion it isn't, but I don't feel strongly.   
  
If it is possible, it would be a pretty interesting tool!  
```  
std::vector<std::function<double(double)>> fns = { ... };  
std::vector<double> input = { ... };  
std::vector<double> output;  
std::ranges::transform(fns, input, back_inserter(output), _1.invoke(_2));  
```  
I wouldn't mind writing that at all.
