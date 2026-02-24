# #258 - `make_primitive` and its `result_type` [Closed]

> Username: Kojoley  
> Created at: 2017-11-03 14:33:07 UTC  
> Updated at: 2017-11-03 16:04:45 UTC  
> Closed at: 2017-11-03 16:04:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/258  

Breezing through spirits internals I found a suspicious thing https://github.com/boostorg/spirit/blob/9a8cded55355644c749d6511504b59c048d64de3/include/boost/spirit/home/qi/meta_compiler.hpp#L31-L47  
  
It looks to me that the `result_type` here is wrong, so the behavior on compilators w/ and w/o decltype support *should* be different. All of `qi`, `karma` and `lex` have this code.  
  
P.S: At fixing spirits examples I have recently figured out a lot of wrong usages of `result_of` protocol so I would not be surprised that this is a hidden error too.

---

## Comment 1

> Username: djowel  
> Created at: 2017-11-03 14:58:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/258#issuecomment-341727763  

Spirit's result protocol predates result_of. I wouldn't be surprised if there were mistakes. Feel free to fire up a PR if you will.

---

## Comment 2

> Username: Kojoley  
> Created at: 2017-11-03 16:04:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/258#issuecomment-341748265  

Ok, `make_primitive::result_type` used directly by `make_component` https://github.com/boostorg/spirit/blob/9a8cded55355644c749d6511504b59c048d64de3/include/boost/spirit/home/qi/meta_compiler.hpp#L75-L78 without using `boost::result_of`.  
  
So while it is a kind of a bug it actually does not matter (until someone is going to touch it).
