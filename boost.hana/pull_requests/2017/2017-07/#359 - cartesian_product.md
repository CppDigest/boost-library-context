# #359 [view] cartesian_product [Merged]

> Username: ricejasonf  
> Created at: 2017-07-03 22:37:46 UTC  
> Updated at: 2017-08-22 01:37:14 UTC  
> Merged at: 2017-08-22 01:37:14 UTC  
> Closed at: 2017-08-22 01:37:14 UTC  
> Url: https://github.com/boostorg/hana/pull/359  

- Adds cartesian_product_view_t and cartesian_product_element_view_t  
    with specializations for the following:  
      - at_impl  
      - is_empty_impl  
      - length_impl  
      - unpack_impl  
  
Note: The name `hana::detail::cartesian_product` is being used because I think we can change namespace `detail` here to `view` to expose these functions. This is important since we can't implement Sequence functions as the view "type" is not technically a Sequence.  
  
We could also then change names like `joined` and `flattened` to simply `view::join` and `view::flatten` so the naming is consistent.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2017-07-04 23:40:58 UTC  
> Updated_at: 2017-07-04 23:42:25 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-312967187  

Please let my note read as a suggestion for   
> Consider providing access to the flattened methods & al.  
  
I don't mean to sound presumptuous. :smile:

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2017-07-07 02:55:59 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-313574037  

@ldionne Have you had a chance to look at this? I think there might be a problem with the fact that `unpack` yields nested views which would make the result of `to_tuple` not be a tuple of tuples.  
  
Perhaps we could add a `to_impl` for Sequences and `view_tag` if you don't think that is a hack.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2017-08-02 05:07:01 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-319568632  

ping! Is this really bad or are you just busy? :stuck_out_tongue:

---

## Comment 4

> Username: ldionne  
> Created_at: 2017-08-02 05:08:23 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-319568787  

The latter. Sorry, let me have a look at this right now.

---

## Review 5 [Commented]

> Username: ldionne  
> Created_at: 2017-08-02 05:44:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/359#pullrequestreview-53699444  

I think this is really good, however the only concern I see with this is that `cartesian_product` is normally associated to the `Sequence` concept. When I initially implemented these views, you'll notice that I almost only provided the minimal complete definitions for some concepts like `Comparable`, `Foldable`, `Iterable`, etc.. Well technically, there's also `sliced_view`, but you can't create it by using `hana::slice`. While this technically does not introduce `hana::cartesian_product` for views, it begs the question of whether we should do it. Before we start adding those methods, I'd like to solve the conceptual problem of having views implement "disparate" methods that are normally associated to a concept, without fulfilling that concept.  
  
Like I said, this PR is fine, but it begs the question of what to do next.

📁 include/boost/hana/cartesian_product.hpp

```diff
 115 |+ 
 116 |+             constexpr auto operator()() const
 117 |+             {
```

> Username: ldionne  
> Created_at: 2017-08-02 05:09:57 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130786734  

Placement of braces

---

📁 include/boost/hana/cartesian_product.hpp

```diff
 121 |+             template <typename X1, typename ...Xs>
 122 |+             constexpr auto operator()(X1&& x1, Xs&& ...xs) const
 123 |+             {
```

> Username: ldionne  
> Created_at: 2017-08-02 05:10:04 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130786741  

x2

---

📁 include/boost/hana/cartesian_product.hpp

```diff
  90 |         };
  91 |+ 
  92 |+         struct make_cartesian_product_indices_helper_t {
```

> Username: ldionne  
> Created_at: 2017-08-02 05:10:26 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130786779  

Okay, that's cool.


📁 include/boost/hana/view.hpp

```diff
  94 |+         template <typename Sequences>
  95 |+         struct is_view<cartesian_product_view_t<Sequences>> {
  96 |+             static constexpr bool value = false;
```

> Username: ldionne  
> Created_at: 2017-08-02 05:13:00 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130786963  

And here too?

---

📁 include/boost/hana/view.hpp

```diff
  71 |+         template <typename Sequences, std::size_t i>
  72 |+         struct is_view<cartesian_product_element_view_t<Sequences, i>> {
  73 |+             static constexpr bool value = false;
```

> Username: ldionne  
> Created_at: 2017-08-02 05:13:04 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130786969  

Wait, don't you mean `true` here?

---

📁 include/boost/hana/view.hpp

```diff
 358 |+         static constexpr decltype(auto)
 359 |+         apply(detail::cartesian_product_view_t<Sequences> view, N const&) {
 360 |+           return detail::cartesian_product_element_view_t<Sequences, N::value>{view.sequences_};
```

> Username: ldionne  
> Created_at: 2017-08-02 05:16:10 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130787224  

Before this point I wondered why you needed this `cartesian_product_element_view_t` type, but now I see.


📁 test/view/cartesian_product/at.cpp

```diff
  18 |+ int main() {
  19 |+     auto container = ::seq;
  20 |+ 
```

> Username: ldionne  
> Created_at: 2017-08-02 05:16:46 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130787279  

This is missing a unit test for an empty sequence, and for a non-empty sequence containing empty sequences (first, second, third, etc..). Please add a few test cases like that. It's not so much because I doubt the correctness of your implementation, but these sort of test cases are very good at making strange bugs surface (e.g. the unary copy-constructors for tuple surfaces several SFINAE-related bugs).

> Username: ricejasonf  
> Created_at: 2017-08-07 17:15:24 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r131712200  

I'm adding these to the `unpack` tests.


📁 test/view/cartesian_product/modifications.cpp

```diff
  22 |+     auto view = hana::detail::cartesian_product(storage);
  23 |+ 
  24 |+     hana::at_c<0>(hana::at_c<0>(view)) = 90;
```

> Username: ldionne  
> Created_at: 2017-08-02 05:19:30 UTC  
> Updated_at: 2017-08-07 18:22:42 UTC  
> Url: https://github.com/boostorg/hana/pull/359#discussion_r130787640  

Oh, it's neat that you can do that. Also kind of scary, but it makes sense.


---

## Comment 6

> Username: ricejasonf  
> Created_at: 2017-08-02 06:13:48 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-319577915  

The point of this was to facilitate `hana::ap`.  
```cpp  
    template <>  
    struct ap_impl<hana::view_tag> {  
        template <typename F, typename X>  
        static constexpr auto apply(F&& f, X&& x) {  
            // TODO: Implement cleverly; we most likely need a cartesian_product  
            //       view or something like that.  
            return hana::ap(hana::to_tuple(f), hana::to_tuple(x));  
        }  
};  
```

---

## Comment 7

> Username: ldionne  
> Created_at: 2017-08-02 06:16:17 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-319578313  

Ahhhhhhhhhhhhhh!!! Sorry! No more concern, only those things I left comments on. Also, I think the `to_tuple` issue is not a big deal. What would you expect?

---

## Comment 8

> Username: ricejasonf  
> Created_at: 2017-08-02 20:15:31 UTC  
> Updated_at: 2017-08-07 18:23:57 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-319785265  

`to_tuple` would yield a `tuple` of views which makes sense but could be a potential pitfall I guess.  
  
I'm not worried about it if you think it will be fine.

---

## Comment 9

> Username: ldionne  
> Created_at: 2017-08-03 02:25:23 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-319850234  

I don't think it's a big deal. Also, I just don't see a good solution.

---

## Comment 10

> Username: ricejasonf  
> Created_at: 2017-08-07 18:27:51 UTC  
> Updated_at: 2017-08-07 18:50:50 UTC  
> Url: https://github.com/boostorg/hana/pull/359#issuecomment-320742866  

Fixups  
  
- Fixes the formatting issues  
- Adds suggested tests, and they did indeed flush out a zero-length array bug.  
- Changes the name `detail::cartesian_product` to `detail::cartesian_product_view`

---
