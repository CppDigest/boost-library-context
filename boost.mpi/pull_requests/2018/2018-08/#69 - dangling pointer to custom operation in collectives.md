# #69 dangling pointer to custom operation in collectives [Merged]

> Username: aminiussi  
> Created at: 2018-08-01 13:10:55 UTC  
> Updated at: 2018-08-02 12:50:12 UTC  
> Merged at: 2018-08-02 12:32:42 UTC  
> Closed at: 2018-08-02 12:32:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/69  

Try to make sure user's operators are stateless, and do not use their state if any.  
  
refs #68

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-08-01 13:11:54 UTC  
> Url: https://github.com/boostorg/mpi/pull/69#issuecomment-409569474  

@mkuron does this fixes the issue ?

---

## Review 2 [Approved]

> Username: mkuron  
> Created_at: 2018-08-02 09:56:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/mpi/pull/69#pullrequestreview-142722675  

This fixes it. Thanks!

📁 include/boost/mpi/collectives/all_reduce.hpp

```diff
  51 |                   T* out_values, Op op, mpl::false_ /*is_mpi_op*/,
```

> Username: mkuron  
> Created_at: 2018-08-02 09:55:45 UTC  
> Updated_at: 2018-08-02 12:32:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/69#discussion_r207168423  

Unused variable `op` here and in the five other places below: replace with `/*op*/`.


---
