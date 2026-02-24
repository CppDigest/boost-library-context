# #42 Mostly GitHub workflow and build system changes [Merged]

> Username: jeremy-murphy  
> Created at: 2025-09-24 09:47:44 UTC  
> Updated at: 2026-02-19 08:21:27 UTC  
> Merged at: 2026-02-19 08:21:27 UTC  
> Closed at: 2026-02-19 08:21:27 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42  

And some fixes to the documentation, and one fix to the code.  
  
Also updated the maintainer to myself, which I should have done a few years ago when I took over Boost.Graph, whoops.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-09-24 10:06:25 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3327568130  

@jzmaddock , are you available to review?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-09-27 12:21:19 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3341614788  

The changes look fine to me.  Any idea what's started causing all the segfaults in the tests?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-09-27 12:21:49 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3341615349  

BTW we could really use an MSVC CI runner too.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-09-30 22:48:11 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3354033047  

> The changes look fine to me. Any idea what's started causing all the segfaults in the tests?  
  
No idea! I'll have to investigate.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2025-10-13 23:10:43 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3399359202  

Very curious that the macos (clang) build passes.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2025-10-15 01:05:52 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3404151531  

@aminiussi maybe you can help here: do you know why these `mpich` builds fail?

---

## Comment 7

> Username: aminiussi  
> Created_at: 2025-10-15 09:08:15 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3405358406  

I test boost.mpi with HPE/Cray MPI which is mpich based so nothing trivial comes to mind.   
  
I only test with cmake and I think there are no cmake based tests for graph parallel, so I'll have to make a b2/gcc13/mpich build on my ubuntu laptop to sees if I can reproduce the issue.

---

## Comment 8

> Username: aminiussi  
> Created_at: 2025-10-15 16:36:52 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/42#issuecomment-3407354417  

I would replace:  
```  
mpi_process_group pg;  
```  
with  
```  
boost::mpi::communicator world{};  
BOOST_TEST(world.size() > 1); // not supposed to work on one proc  
mpi_process_group pg{world};  
```  
Then, I suspect the test will fail the `BOOST_TEST(world.size() > 1)`  check as the communicator size is  1 (and that is the real problem).  
  
Since the mpi_process_group class allocate one incomming and outgoing communication structures in containers of size comm.size(), sending a message to proc 1 will retrieve a non existing outgoing structure   (in mpi_process_group.ipp:173).  
  
I suspect the problem is in the project_config.jam generated file that does not actually select the mpich implementation, which ends up launching 2 singleton instead of two process MPI jobs.

---
