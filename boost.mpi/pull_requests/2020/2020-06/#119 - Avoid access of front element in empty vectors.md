# #119 Avoid access of front element in empty vectors [Merged]

> Username: fweik  
> Created at: 2020-06-10 11:19:55 UTC  
> Updated at: 2020-06-19 10:46:37 UTC  
> Merged at: 2020-06-19 10:43:32 UTC  
> Closed at: 2020-06-19 10:43:32 UTC  
> Url: https://github.com/boostorg/mpi/pull/119  

There are multiple places in the library where a pointer to the memory managed by a vector by taking the address of the first element. This leads to undefined behavior if the vector is empty (eg there is no first element), which has caused problems downstream. For example some standard libraries have asserts for this. This patch is a modified version of a patch created by @mkuron which switches all the places that we found to `detail::c_data`, which already existed in the library.  
  
(Please note that not all of the replacements are needed because in some places the vector can not  
be empty, but I find the function call also more idiomatic and readable, and easier to replace when support for C++03 is dropped)

---

## Comment 1

> Username: mkuron  
> Created_at: 2020-06-10 11:23:14 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-641937829  

Just want to mention that this patch makes lots more places in Boost.MPI benefit from the fix introduced in #81.

---

## Comment 2

> Username: jngrad  
> Created_at: 2020-06-10 17:43:50 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-642160004  

I can confirm this PR fixes the assertion error blocking [1843105](https://bugzilla.redhat.com/show_bug.cgi?id=1843105) on Fedora 33 for OpenMPI & MPICH.

---

## Comment 3

> Username: pdimov  
> Created_at: 2020-06-11 02:06:12 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-642361614  

A test hangs, which is why the jobs time out; splitting them doesn't help with that. Add `-l 240` to the `b2` invocation (timeout in seconds, may need to vary the 240 appropriately, I'm not sure how long the tests normally take) to see which test.

---

## Review 4 [Commented]

> Username: jwakely  
> Created_at: 2020-06-11 09:56:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpi/pull/119#pullrequestreview-428763810  

📁 src/cartesian_communicator.cpp

```diff
  18 |   template <typename T, typename A>
  18 |-   T* c_data(std::vector<T,A>& v) { return &(v[0]); }
  19 |+   T* c_data(std::vector<T,A>& v) { return c_data(v); }
```

> Username: jwakely  
> Created_at: 2020-06-11 09:56:39 UTC  
> Updated_at: 2020-06-11 13:03:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#discussion_r438674291  

Isn't this going to call itself recursively forever?  
```diff  
-  T* c_data(std::vector<T,A>& v) { return c_data(v); }  
+  T* c_data(std::vector<T,A>& v) { return detail::c_data(v); }  
```

> Username: jwakely  
> Created_at: 2020-06-11 10:26:18 UTC  
> Updated_at: 2020-06-11 13:03:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#discussion_r438689624  

It might be simpler just to replace this function with `using detail::c_data;`

> Username: aminiussi  
> Created_at: 2020-06-11 12:29:55 UTC  
> Updated_at: 2020-06-11 13:03:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#discussion_r438746584  

> Isn't this going to call itself recursively forever?  
  
A legitimate concern.

> Username: fweik  
> Created_at: 2020-06-11 12:51:56 UTC  
> Updated_at: 2020-06-11 13:03:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#discussion_r438758470  

This not a recursive call, it's `boost::mpi::<anonymous namespace>::c_data` calling `boost::mpi::detail::c_data`. But I'll remove the former.

> Username: jwakely  
> Created_at: 2020-06-11 12:55:29 UTC  
> Updated_at: 2020-06-11 13:03:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#discussion_r438760453  

How is it going to call `detail::c_data`? Name lookup inside the function finds that function itself, not one that isn't even visible.

> Username: fweik  
> Created_at: 2020-06-11 12:57:00 UTC  
> Updated_at: 2020-06-11 13:03:31 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#discussion_r438761347  

Ah sorry, I confused your suggested change with the diff. You are right of course.


---

## Comment 5

> Username: fweik  
> Created_at: 2020-06-11 12:55:13 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-642627690  

The timeout were there before, e.g. #117 and all the other open PRs. Unfortunately there is no easy way to run the tests locally, so I think it's to cumbersome for me to debug this.

---

## Comment 6

> Username: fweik  
> Created_at: 2020-06-11 13:06:16 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-642635510  

Let me know if I should revert 1b2b7bb...

---

## Comment 7

> Username: aminiussi  
> Created_at: 2020-06-19 09:51:44 UTC  
> Updated_at: 2020-06-19 10:10:33 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-646546023  

> Let me know if I should revert [1b2b7bb](https://github.com/boostorg/mpi/commit/1b2b7bbb11ee3bdefe5899d336085ca4d0b2bd58)...  
  
I'll merge that one individually  
  
Thanks

---

## Comment 8

> Username: aminiussi  
> Created_at: 2020-06-19 10:46:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/119#issuecomment-646568979  

> A test hangs, which is why the jobs time out; splitting them doesn't help with that. Add `-l 240` to the `b2` invocation (timeout in seconds, may need to vary the 240 appropriately, I'm not sure how long the tests normally take) to see which test.  
  
Can't reproduce: I've got all test passing on OpenMPI 4.0.0 and 4.0.4 (g++7.5.0) and Intel's 2020 compiler and MPI.  
  
And none of them took that long.

---
