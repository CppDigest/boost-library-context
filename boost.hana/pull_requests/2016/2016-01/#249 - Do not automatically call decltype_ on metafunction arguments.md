# #249 [Metafunction] Do not automatically call decltype_ on metafunction arguments [Closed]

> Username: ldionne  
> Created at: 2016-01-30 16:39:23 UTC  
> Updated at: 2016-02-15 15:13:11 UTC  
> Closed at: 2016-02-15 15:12:13 UTC  
> Url: https://github.com/boostorg/hana/pull/249  

This PR would close #73, but I am not sure whether it should be done. My initial concerns for metafunctions automatically using `decltype_` on their arguments were  
1. Compile-time performance, since this adds some work to do just to invoke a metafunction. Also, I was concerned that this would make it more difficult to write efficient higher-order algorithms on a sequence specialized for types (as introduced by #248), since invoking a metafunction would now have to go through `hana::decltype_`.  
2. Loss of generality, since we lose the ability to call metafunctions with `hana::type`s as arguments (i.e. `f<hana::type<T>>` is now impossible, it will always be `f<T>`).  
3. More magic going on under the hood.  
  
However, the obvious upside of this is convenience on the user side when working with mixed types and values. On the other hand, perhaps the magic going on under the hood can actually make it more difficult to use.  
  
These issues must be considered before merging or discarding this.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-02-15 15:11:10 UTC  
> Url: https://github.com/boostorg/hana/pull/249#issuecomment-184247667  

I will merge this for the time being, since it is more conservative not to provide this sugar. This will give us more flexibility to implement optimizations for type-only algorithms and data structures. Then, if we realize that it's reasonable to provide this sugar, it will be easy to do so in a (almost) backwards-compatible manner later on. The only things that will break if we do the change later on are calls of the form `hana::decltype_(hana::type_c<T>)`, which will become equivalent to `hana::type<T>` instead of `hana::type<hana::type<T>>`. I don't think this is a major problem.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-02-15 15:12:13 UTC  
> Url: https://github.com/boostorg/hana/pull/249#issuecomment-184248023  

Closed by b1696fe0cc081c2297c06e0dc60176bca74769d2.

---
