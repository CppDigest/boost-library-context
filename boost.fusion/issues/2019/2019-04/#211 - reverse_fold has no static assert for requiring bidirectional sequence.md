# #211 - reverse_fold has no static assert for requiring bidirectional sequence [Open]

> Username: Kojoley  
> Created at: 2019-04-01 19:07:27 UTC  
> Updated at: 2019-04-02 17:03:12 UTC  
> Url: https://github.com/boostorg/fusion/issues/211  

~~The example from documentation https://www.boost.org/doc/libs/1_69_0/libs/fusion/doc/html/fusion/algorithm/iteration/functions/reverse_fold.html compiles fine as-is, but replacing `vector` with `cons` yield a compile error. This seems to be a regression, because I came to it at debugging failure in Proto tests, but it might be introduced a long time ago.~~  
  
Update: the Proto test/example is simply violating Fusion invariants. I opened a PR to fix it https://github.com/boostorg/proto/pull/15.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-04-01 19:46:47 UTC  
> Url: https://github.com/boostorg/fusion/issues/211#issuecomment-478719631  

It turns out that Fusion is fine, [`reverse_fold`](https://www.boost.org/doc/libs/1_69_0/libs/fusion/doc/html/fusion/algorithm/iteration/functions/reverse_fold.html) requires a `Bidirectional Sequence` while [`cons`](https://www.boost.org/doc/libs/1_69_0/libs/fusion/doc/html/fusion/container/cons.html) is a `Forward Sequence`, though it could have a static assert like `reverse_view` has  https://github.com/boostorg/fusion/blob/afb8b150a9ae4101fe94bb1bb7004ec06d0cd33f/include/boost/fusion/view/reverse_view/reverse_view.hpp#L48-L51
