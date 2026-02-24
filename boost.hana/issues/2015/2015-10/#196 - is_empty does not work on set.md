# #196 - is_empty does not work on set [Open]

> Username: ldionne  
> Created at: 2015-10-29 23:00:06 UTC  
> Updated at: 2015-10-29 23:00:06 UTC  
> Url: https://github.com/boostorg/hana/issues/196  

This is because a `set` is not an `Iterable`. `is_empty` should probably move to `MonadPlus`, and `set` might be a model of `MonadPlus` using `union_`.
