# #74 - include/boost/mpi/seq.hpp on master only? [Closed]

> Username: pdimov  
> Created at: 2018-10-07 14:47:48 UTC  
> Updated at: 2018-10-07 18:06:01 UTC  
> Closed at: 2018-10-07 17:18:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/74  

In the course of adding Travis support to `mpi` (so that I can later add it to `graph_parallel`) I looked at whether `master` and `develop` were in a state in which I could merge the Travis changes to `master`. They aren't because on `develop`, there's unmerged work from September. But there's something else too; the file `include/boost/mpi/seq.hpp` sits on `master` but is not on `develop`, and I'm not sure why.  
  
(I tried to linearize the history by merging master to develop, an operation which should typically do nothing on the contents of the repo, and this file appeared all of a sudden.)

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-10-07 15:36:20 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427662474  

Right now, master is updated through merges from develop. But before that, I've heard it was updated through cherry picking. So maybe some weird stuff happened at that point.  
I was away from boost the last 2 weeks but I'll have a look into it.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-07 15:42:22 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427662936  

No, that's newer, the file is (c) 2018 you: https://github.com/boostorg/mpi/blob/master/include/boost/mpi/seq.hpp  
  
Its history (https://github.com/boostorg/mpi/commits/master/include/boost/mpi/seq.hpp) says that it appeared as a result of this commit:  
  
https://github.com/boostorg/mpi/commit/30f32d531932809e3122fdeebbea629023854eeb  
  
No idea what this is.

---

## Comment 3

> Username: aminiussi  
> Created at: 2018-10-07 15:56:15 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427663926  

Oups, that's not even supposed to be on develop, this is part of what is barely a pet project for now and not even related to #58.  
  
I'll look into it right away, with any luck it's not included

---

## Comment 4

> Username: pdimov  
> Created at: 2018-10-07 16:05:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427664691  

It's not on develop. It's on master. :-)

---

## Comment 5

> Username: pdimov  
> Created at: 2018-10-07 16:07:10 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427664800  

Either way, Travis is now on, and it's all green: https://travis-ci.org/boostorg/mpi/builds/438279998 :-)  
  
I used OpenMPI because I had no idea which one should I use. Trusty also seems to have mpich.

---

## Comment 6

> Username: aminiussi  
> Created at: 2018-10-07 16:07:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427664833  

What I meant is that it was not even supposed to be on develop, let alone master :-)

---

## Comment 7

> Username: aminiussi  
> Created at: 2018-10-07 16:16:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427665511  

Any conforming C MPI with MPI2 compatibility should be ok. Intel MPI (mpich based) is the one I used on day to day basis because that's what I have on my clusters. OpenMPI might bring in more than what is strictly required (by default, it automatically bring in the legacy C++ API) but that shouldn't hurt.  
  
Thanks a lot !

---

## Comment 8

> Username: aminiussi  
> Created at: 2018-10-07 17:19:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427670259  

merging master on develop is now a no-op.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-10-07 18:06:01 UTC  
> Url: https://github.com/boostorg/mpi/issues/74#issuecomment-427673782  

`graph_parallel` has Travis too now: https://travis-ci.org/boostorg/graph_parallel/builds/438311307  
  
One of the tests hangs on g++-8, but only in C++03 mode:  
  
```  
capture-output bin.v2/libs/graph_parallel/test/distributed_csr_algorithm_test-1.test/gcc-8.1.0/release/cxxstd-03-iso/visibility-hidden/distributed_csr_algorithm_test-1-run  
300 second time limit exceeded  
```  
  
Not sure how to go about identifying the cause. I'll leave it that way for now.
