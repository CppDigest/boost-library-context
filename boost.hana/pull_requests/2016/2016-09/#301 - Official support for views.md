# #301 Official support for views [Open]

> Username: ldionne  
> Created at: 2016-09-28 21:20:10 UTC  
> Updated at: 2021-03-31 11:05:15 UTC  
> Url: https://github.com/boostorg/hana/pull/301  

The goal of this PR is to officially support views, which are only experimental right now.  
  
Still left to do:  
- [x] Move view types out of the `experimental` namespace, perhaps into `detail`  
- [ ] ~~Consider providing access to the `flattened` methods & al.~~ _(not at first)_  
- [x] Test nested views  
- [ ] Add examples  
- [ ] Document views in the tutorial (requires some rewording of the sections on containers and on algorithms)  
- [ ] ~~Provide operators~~ _(not at first)_  
- [x] Provide a non-dummy implementation of `hana::equal` (thanks @ricejasonf)  
- [x] Provide a non-dummy implementation of `hana::ap` (thanks @ricejasonf)  
- [ ] ~~Consider providing a `filtered_view`~~ _(not at first)_

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2017-07-01 19:32:53 UTC  
> Updated_at: 2017-07-01 19:33:28 UTC  
> Url: https://github.com/boostorg/hana/pull/301#issuecomment-312451466  

I have a PR for `equal`, and I'd like to do `ap` as well. It appears that `detail::cartesian_product_indices` provides an interface that should make that easy to implement. A `cartesian_product` view looks pretty easy with that as well.

---

## Comment 2

> Username: ldionne  
> Created_at: 2017-12-02 02:46:06 UTC  
> Url: https://github.com/boostorg/hana/pull/301#issuecomment-348662400  

I'm striking a few things that I marked as being required to ship this PR. Given my lack of time to work on Hana (very sadly), I'm going to go for a minimal implementation for the sake of shipping this. This PR is important and it has been sitting around for way too long.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2017-12-02 03:15:24 UTC  
> Url: https://github.com/boostorg/hana/pull/301#issuecomment-348663898  

I didn't actually do `ap` yet, but it should be trivial now.  
  
I could help with tests too if that is welcome.

---

## Comment 4

> Username: ldionne  
> Created_at: 2017-12-02 03:54:33 UTC  
> Url: https://github.com/boostorg/hana/pull/301#issuecomment-348665845  

I think the tests for `ap` are okay, what I'm mostly concerned about is tests for the rest of the functionality. For example, `filter` _should_ work currently because `view_tag` implements `MonadPlus`, but I'm fairly confident it won't work properly (either compilation error or dangling reference) based on looking at how `single_view` is implemented.  
  
So yeah, I'd welcome help on making the tests more robust. I'm happy to tackle the documentation side.

---

## Comment 5

> Username: ricejasonf  
> Created_at: 2018-01-03 19:49:25 UTC  
> Updated_at: 2018-01-03 19:57:55 UTC  
> Url: https://github.com/boostorg/hana/pull/301#issuecomment-355108737  

Are we going to make the views `Sequence` or is that some kind of violation?  
    
(can't remember if I already asked this)

---

## Comment 6

> Username: ldionne  
> Created_at: 2018-01-04 17:52:17 UTC  
> Updated_at: 2018-01-04 17:53:18 UTC  
> Url: https://github.com/boostorg/hana/pull/301#issuecomment-355351387  

They can't be `Sequence`s because there's no way to `make` them from a pack of elements. In other words, they don't have storage like a Container, and `Sequence` requires that (actually `Sequence` might be better named `Container`, since it reflects element ownership).

---
