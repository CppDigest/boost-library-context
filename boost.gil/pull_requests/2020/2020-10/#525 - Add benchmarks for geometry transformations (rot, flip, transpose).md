# #525 Add benchmarks for geometry transformations (rot, flip, transpose) [Open]

> Username: sdebionne  
> Created at: 2020-10-20 17:05:57 UTC  
> Updated at: 2023-07-28 07:43:59 UTC  
> Url: https://github.com/boostorg/gil/pull/525  

### Description  
  
Add benchmarks for geometry transformations with additional benchmarks for reference libraries such as OpenCV, IPP or Blaze for the transpose transformation.  
  
### References  
  
Preliminary results were discussed on [Slack](https://cpplang.slack.com/archives/CSVT0STV2/p1602666000037100)  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-10-22 18:58:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/525#pullrequestreview-515024366  

This looks very good to me.  
  
Is this still draft or shall I merge or do you prefer to merge yourself?

📁 benchmark/google/view_transpose_impl.cpp

```diff
  30 |+             });
  31 |+ 
  32 |+ #if GIL_ENABLE_UNROLLED
```

> Username: mloskot  
> Created_at: 2020-10-22 18:51:41 UTC  
> Updated_at: 2020-10-22 18:58:30 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510384376  

We should prefix all our macros with `BOOST_GIL_*`, see https://github.com/boostorg/gil/issues/410  
  
I'd suggest to rename this to `BOOST_GIL_BENCHMARK_ENABLE_UNROLLED` or similar.

---

📁 benchmark/google/view_transpose_impl.cpp

```diff
  14 |+     namespace gil {
  15 |+ 
  16 |+         template <typename SrcView, typename DstView>
```

> Username: mloskot  
> Created_at: 2020-10-22 18:53:55 UTC  
> Updated_at: 2020-10-22 18:58:30 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510385670  

Nitpick: we don't indent content of the namespace  
  
However, hand-rolled formatting consistency is not that important for benchmark, so it can wait for the `.clang-format` overhaul.


📁 benchmark/google/blaze/view_transpose.cpp

```diff
  37 |+     auto mat_out = as_matrix(view(in));
  38 |+ 
  39 |+     for (auto _ : state) {
```

> Username: mloskot  
> Created_at: 2020-10-22 18:55:11 UTC  
> Updated_at: 2020-10-22 18:58:30 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510386409  

Is this `auto` idiomatic Google Benchmark, and not auto&&` for example?  
Does that make any difference for the timing?

> Username: sdebionne  
> Created_at: 2020-10-23 07:43:25 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510695529  

Not sure it's idiomatic but every examples I looked at are using `for (auto _ : state) {`

> Username: mloskot  
> Created_at: 2020-10-23 08:14:24 UTC  
> Updated_at: 2020-10-23 08:14:25 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510712437  

Good, thanks.


📁 benchmark/google/ipp/view_flip.cpp

```diff
  66 |+     using namespace boost::gil;
  67 |+ 
  68 |+     size_t dim = state.range(0);
```

> Username: mloskot  
> Created_at: 2020-10-22 18:56:14 UTC  
> Updated_at: 2020-10-22 18:58:30 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510387032  

Interesting, it does not require `std::size_t` as we have no `using namespace std`.

> Username: sdebionne  
> Created_at: 2020-10-23 07:42:45 UTC  
> Updated_at: 2020-10-23 07:42:46 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510695187  

`size_t` is also defined in C, but I will change to `std::size_t`.

> Username: mloskot  
> Created_at: 2020-10-23 08:14:10 UTC  
> Url: https://github.com/boostorg/gil/pull/525#discussion_r510712302  

Yes, it is. I just trusted it comes from `<cstdlib>` or `<cstddef>` or other `<c...>` headers, then it is imported into `std` namespace.  
  
However, I think it's better to refer to the C type aliases via `std::` to help fussy compilers.


---
