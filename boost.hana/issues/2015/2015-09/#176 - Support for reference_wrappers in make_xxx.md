# #176 - Support for reference_wrappers in make_xxx [Open]

> Username: ldionne  
> Created at: 2015-09-08 19:49:37 UTC  
> Updated at: 2018-07-23 04:10:36 UTC  
> Url: https://github.com/boostorg/hana/issues/176  

Right now, containers are documented as not supporting references at all. First, this should be changed because most containers are able to hold references. Furthermore, it would be nice to add support for `reference_wrapper` in `make_tuple`, like `std::make_tuple`. Specifically, `hana::make_tuple(std::ref(x))` should create a `hana::tuple<X&>` instead of a `hana::tuple<std::reference_wrapper<X>>`.  
  
Things I'm worried about:  
- Compile-time cost of adding even more machinery to `make_tuple`. This will have to be benchmarked to be sure.  
- Compile-time cost of including `<functional>` in such a fundamental header. This has to be benchmarked too, and we can probably cheat with a forward declaration otherwise.  
  
As a motivating example for this feature, consider the following:  
  
``` c++  
struct Person {  
    std::string name;  
};  
  
auto xs = hana::make_tuple(Person{"Bob"}, Person{"John"});  
auto ys = hana::transform(xs, [](Person& p) {  
    return std::ref(p.name);  
});  
  
// Right now, ys contains reference_wrappers, which is hard to manipulate.  
// This feature would give us the desired intuitive behavior.  
```  
  
Containers that should probably support this feature if we decide to go forward:  
- `tuple`  
- `set`  
- `map`  
- `pair`  
- `optional`  
- `lazy`?  
  
As for `basic_tuple`, I think it is better to keep it as basic as possible for performance reasons. I don't think anybody using `basic_tuple` would expect that kind of sugar anyway.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-13 15:22:47 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-139887062  

After benchmarking the compile-time cost of adding support for this,  
- Adding additional machinery to `make_tuple` is fine.  
- Including `<functional>` just to get the forward declaration of `std::reference_wrapper` is _not_ fine. It adds about 0.2s of compile-time when just including `hana/tuple.hpp`. Also, note that we can't forward declare stuff in the `std::` namespace, so we can't just forward declare `std::reference_wrapper` ourselves. And this is not only a nitpick; for example, libc++ defines its stuff in the `std::__1` inline namespace, so it really does not work to forward declare in `std::`.  
  
This is really lame; all we want is a `<functional_fwd>` header, but we really can't take the hit to include the whole `<functional>` header, especially since that header is not likely to be included by users on their own anyway, unlike `<type_traits>` for example. I see two solutions:  
1. We forward declare `std::reference_wrapper` in the `std::` namespace, and make sure it works on libc++ and libstdc++. For this, we will need to keep the forward declarations in sync with the standard libraries. This is hacky.  
2. We declare our own `hana::reference_wrapper`, which stupidly reinvents the wheel and forces users to use our own instead of `std`'s. This is also unsatisfactory.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-09-13 17:13:25 UTC  
> Updated at: 2015-09-13 17:13:48 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-139896326  

> This is really lame; all we want is a `<functional_fwd>` header, but we really can't take the hit to include the whole `<functional>` header  
  
@ericniebler, I saw that you also forward declare stuff in namespace `std` manually in your Meta library. Do you think it would be worth it to try and add forward declaration headers to the standard library? Do you think that might _ever_ pass on the standard committee? It seems like this would be useful.

---

## Comment 3

> Username: badair  
> Created at: 2015-11-19 09:36:55 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-158003718  

> I see two solutions: ...  
  
Perhaps there is a third (still far from ideal):  
  
What if we allow the client to pass a reference_wrapper-like template as a template template arg? This is not ideal for a number of reasons, and I'm not well-versed in the performance implications of this (might be really bad, idk), but at a glance it seems like this might strike a balance between performance and usability. It would certainly add some unfortunate complexity to the code.  
  
Sure, nobody really wants to pass std::reference_wrapper as a template arg, but nobody wants to use a hana::reference_wrapper shim or wait for a standardized forward declaration either.  
  
Is that a plausible solution?

---

## Comment 4

> Username: badair  
> Created at: 2015-11-19 09:39:23 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-158004634  

Edit: I just realized there is an open PR for this. I'll check it out later (I'm on mobile so I can't easily edit my above comment).

---

## Comment 5

> Username: ldionne  
> Created at: 2015-11-19 18:37:18 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-158149921  

@badair I think this is not necessarily a bad solution. However, this issue is stalled because of the problems documented in https://github.com/boostorg/hana/pull/179#issuecomment-139915710 more than those documented above.

---

## Comment 6

> Username: badair  
> Created at: 2016-02-02 04:22:49 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-178364037  

What are your thoughts on wrapping `<functional>` dependent code with an `#ifdef`, for those who cannot or wish not to include `<functional>`?

---

## Comment 7

> Username: ldionne  
> Created at: 2016-02-02 12:57:03 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-178560153  

I think it is not necessary to take this burden upon ourselves. `<functional>` is a standard header, and it is provided by all implementations I know of. My opinion is that we should strive _not_ to include `<functional>` in the library for compile-time performance reasons, but if we decide to do so (e.g. because we have no other choice), then I think users should have no control over it. Defining a macro just for this would be adding an additional customization point, thus making the library more complex to use and maintain, for little benefit.

---

## Comment 8

> Username: ericniebler  
> Created at: 2016-02-02 15:44:20 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-178645222  

@ldionne +1

---

## Comment 9

> Username: FrankHB  
> Created at: 2018-07-23 04:10:36 UTC  
> Url: https://github.com/boostorg/hana/issues/176#issuecomment-406933744  

I meet exactly the same issue about cost of inclusion of `<functional>` in my own library (and I know [forward declaration is a no-go because it is not conforming](https://groups.google.com/a/isocpp.org/forum/#!topic/std-discussion/6VNskbSBRIs)). The worse thing is now [`std::invoke_wrapper` in `<type_traits>`](http://eel.is/c++draft/meta.type.synop) is required [to handle `std::reference_wrapper` correctly](http://eel.is/c++draft/func.require#1) and I am simulating that feature in [my library](https://github.com/FrankHB/YSLib/blob/master/YBase/include/ystdex/type_traits.hpp), it is unwise to use header names different to the standard which requires additional work for compatibility. I wonder whether it is considered by the committee...
