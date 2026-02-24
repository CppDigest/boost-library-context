# #834 Allow kernel argument to take custom struct by value [Open]

> Username: rosenrodt  
> Created at: 2019-05-09 10:47:23 UTC  
> Updated at: 2019-05-10 00:42:40 UTC  
> Url: https://github.com/boostorg/compute/pull/834  

By specializing `template<T> struct set_kernel_arg` when expanding BOOST_COMPUTE_ADAPT_STRUCT, kernel input argument can now take custom struct by value. This potentially utilizes constant memory of the compute device

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-05-09 15:06:55 UTC  
> Updated_at: 2019-05-09 17:21:31 UTC  
> Url: https://github.com/boostorg/compute/pull/834#issuecomment-490943425  

[![Coverage Status](https://coveralls.io/builds/23290221/badge)](https://coveralls.io/builds/23290221)  
  
Coverage remained the same at 84.019% when pulling **e1b96b36457997087952df710ceb722ff4ca5e8f on rosenrodt:pr-kernel-pass-struct-by-value** into **36c89134d4013b2e5e45bc55656a18bd6141995a on boostorg:master**.

---

## Comment 2

> Username: rosenrodt  
> Created_at: 2019-05-10 00:42:40 UTC  
> Url: https://github.com/boostorg/compute/pull/834#issuecomment-491114610  

It looks like [Apple platform](https://travis-ci.org/boostorg/compute/jobs/530315064) regards custom struct as invalid kernel argument. That's weird because my 2016 Macbook works just fine

---
