# #114 Remove Boost.MPI-specific python-tag code [Merged]

> Username: SoapGentoo  
> Created at: 2019-12-25 11:33:02 UTC  
> Updated at: 2020-02-26 12:16:50 UTC  
> Merged at: 2020-02-17 20:11:24 UTC  
> Closed at: 2020-02-17 20:11:25 UTC  
> Url: https://github.com/boostorg/mpi/pull/114  

@pdimov @Lastique

---

## Comment 1

> Username: SoapGentoo  
> Created_at: 2020-02-17 11:09:26 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-586942273  

@pdimov can we merge this?

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-02-17 11:30:48 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-586950247  

Could you describe the effect of this change?

---

## Comment 3

> Username: SoapGentoo  
> Created_at: 2020-02-17 11:31:39 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-586950593  

to remove the code duplication that is now in central boost build

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-02-17 11:46:55 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-586956860  

So there will be no change in the build output?

---

## Comment 5

> Username: SoapGentoo  
> Created_at: 2020-02-17 11:49:10 UTC  
> Updated_at: 2020-02-17 11:49:51 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-586958066  

nope, except that Boost.MPI can now be compiled in the presence of multiple python impls

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-02-17 11:54:29 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-586960172  

Ok, thanks.

---

## Comment 7

> Username: pdimov  
> Created_at: 2020-02-17 16:00:04 UTC  
> Url: https://github.com/boostorg/mpi/pull/114#issuecomment-587059459  

Merging this (or not) is up to MPI's maintainer.

---
