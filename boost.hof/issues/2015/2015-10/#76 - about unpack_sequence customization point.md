# #76 - about unpack_sequence customization point [Open]

> Username: viboes  
> Created at: 2015-10-04 17:13:24 UTC  
> Updated at: 2015-10-09 16:45:48 UTC  
> Url: https://github.com/boostorg/hof/issues/76  

I'm not completely against the way you have customized unpack, but I prefer to overload non-member functions than using some kind of traits.   
  
I would preferred if `std::experimental::apply` could be customizable and so   
  
```  
unpack(f)(product_type) <==> std::tuple::apply(f, product_type)  
```  
  
This has the advantage that the user can use the `apply` function when it has the function and the product-type.  
  
What about replacing `unpack_sequence` by an overloaded `apply`?  
  
Overloading `apply` has the liability that it is not a high-order function, but we can have an `apply_fn` that is hof.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 07:21:38 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-145447950  

> What about replacing unpack_sequence by an overloaded apply?  
  
That would be confusing since `fit::apply` calls a function with its parameters and the overload `apply` unpacks the sequence. I could add an ADL name for unpacking, but I need to think of a good name for it.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 21:59:21 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-145680272  

Agreed we may need two different names. As `apply` is already taken by the standard, maybe we need a name for your `apply`

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 22:28:02 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-145687298  

Using the name `apply` here has a long history in C++. There are similar constructs in other libraries, such as Boost.MPL, Meta, Boost.Hana and Pstade.Egg. I would prefer to stick with the commonly used name of `apply` here as it will be familiar with more C++ programmers. Plus, at this point, `std::experimental::apply` is just a TS and not part of the standard yet.   
  
Also, Fit will already unpack tuples, so there is no need to try and integrate with an `apply` overload. Plus, I would not choose a simple name like `apply` for ADL overload because its too easy for things to go wrong with such a simple name(and the fact it used for another purpose in other libraries).

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 23:57:34 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-145701299  

It is up to you to choose the design of your library. What is the apply signature in  Boost.Hana?

---

## Comment 5

> Username: ldionne  
> Created at: 2015-10-06 00:06:16 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-145702341  

In Hana, `apply` is used as `apply(f, args...)`, and it can take an arbitrary [Callable](http://en.cppreference.com/w/cpp/concept/Callable) object. In other words, it is strictly equivalent to `std::invoke`. The exact signature of `apply` is nasty to write here without LaTeX.

---

## Comment 6

> Username: pfultz2  
> Created at: 2015-10-06 16:38:49 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-145922137  

Now that I think about it more. I don't think I will add an ADL customization point, because it makes it harder to detect if a sequence is unpackable. I think I will stick with template specialization for now. It has an extra parameter to use for matching with SFINAE as well, so its easy enough to make it match some user defined function, if the user so wishes.

---

## Comment 7

> Username: pfultz2  
> Created at: 2015-10-09 16:42:18 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-146926074  

Well, thinking about this even more, an ADL customization point could be added, but it would have a different signature. Instead the ADL function would return a function that can unpack the sequence, something like `unpacker(sequence)(f)`. Then this would still make is easy to detect if the sequence is unpackable by checking if `unpacker(sequence)` is callable. Of course, a better name than `unpacker` would be nice.

---

## Comment 8

> Username: pfultz2  
> Created at: 2015-10-09 16:45:48 UTC  
> Url: https://github.com/boostorg/hof/issues/76#issuecomment-146927072  

In addition, one problem with this is approach is that forwarding references can't be used. It might be better to have it called like this: `unpacker(sequence)(sequence, f)` instead. Although it might seem redundant, it would allow the unpacker to use forwarding reference instead of defining three overloads.
