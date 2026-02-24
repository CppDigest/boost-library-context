# #49 - Interest in tuple_transform? [Closed]

> Username: HDembinski  
> Created at: 2020-05-17 11:25:48 UTC  
> Updated at: 2020-05-24 14:19:07 UTC  
> Closed at: 2020-05-24 14:19:07 UTC  
> Url: https://github.com/boostorg/mp11/issues/49  

I would like to contribute a tuple_transform. It accepts a tuple and a callable and constructs a new tuple from the callable applied to each element of the tuple. It works like `construct_from_tuple`, but transforms each element (value and/or type).

---

## Comment 1

> Username: pdimov  
> Created at: 2020-05-17 13:24:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/49#issuecomment-629798137  

Yes please. This is something I intended to add.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-05-20 13:54:54 UTC  
> Url: https://github.com/boostorg/mp11/issues/49#issuecomment-631488835  

Ok, I am on it.

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-05-20 14:45:41 UTC  
> Updated at: 2020-05-20 14:46:05 UTC  
> Url: https://github.com/boostorg/mp11/issues/49#issuecomment-631520233  

You are probably already aware of this: the straight-forward implementation has the caveat that the evaluation order of the functor on the arguments is platform-dependent. I noticed that it changed between gcc versions, for instance. I don't have a solution for that.  
  
The trick of using an initializer_list does not work, because we don't have a collection of identical types.

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-05-20 16:42:32 UTC  
> Url: https://github.com/boostorg/mp11/issues/49#issuecomment-631592028  

See #50 for a draft of the feature
