# #267 - Properties of folds [Open]

> Username: ldionne  
> Created at: 2016-04-20 01:10:56 UTC  
> Updated at: 2016-04-20 01:10:56 UTC  
> Url: https://github.com/boostorg/hana/issues/267  

This is an old note I left myself. I was wondering whether we should require that `xs == ys` implies `fold_left(xs, state, f) == fold_left(ys, state, f)`. From a mathematical perspective, this would be the same as requesting that `fold_left` is a mathematical function, which makes sense. But for sets, which are currently foldable, this wouldn't be satisfied unless `f` satisfies some strong condition (namely that we can fold with it in any order and it's going to give the same result).  
  
This applies equally to `fold_left` and `fold_right`.
