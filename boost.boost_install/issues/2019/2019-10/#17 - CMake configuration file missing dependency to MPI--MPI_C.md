# #17 - CMake configuration file missing dependency to MPI::MPI_C [Closed]

> Username: sdebionne  
> Created at: 2019-10-25 12:53:16 UTC  
> Updated at: 2019-10-28 12:40:40 UTC  
> Closed at: 2019-10-28 08:10:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17  

The CMake configuration files generated at installation with [boostorg/boost_install](https://github.com/boostorg/boost_install) have an imported target that is missing a dependency to the underlying MPI library. The symptoms are an invalid linking order and the associated undefined symbols errors.  
  
@pdimov, what would be the right way to have something like `find_dependency(MPI)` and `target_link_libraries(Boost::mpi PUBLIC MPI::MPI_C)` added to the config file? Shall we add a test for MPI to the boost_install test suite?

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-25 14:09:39 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546369864  

Yes, adding a test will certainly be a required part of the solution.

---

## Comment 2

> Username: aminiussi  
> Created at: 2019-10-25 14:47:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546384458  

As for now, there still might be a problem with open MPI:  
  
When including mpi.h, most the OpenMPI distribution I've found so far insist on detecting a C++ compiler, thus including the legacy C++ headers, which in turn brings in some (unused) C++ MPI symbols.  
  
And I think OpenMPI is the most widespread  MPI implementation.  
  
I'm planing to explicitly prohibit that by default (#73) but untill then... (I'll try to do it this we at least on develop).   
  
I have no experience with  [boostorg/boost_install](https://github.com/boostorg/boost_install) yet.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-25 15:29:55 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546400794  

I'll transfer this issue to boost_install.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-10-25 15:34:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546402489  

As far as I can see, what's needed is https://github.com/boostorg/boost_install/commit/63c74f64562b4789781ab03d59ae38f81e70b9f8.  
  
It's not possible to use PUBLIC for an imported library, only INTERFACE linking is allowed, but I hope CMake does the right thing with those.  
  
I don't have a way to test that at present; it's on the `feature/add-mpi-dependency` branch. I'll merge it to develop upon confirmation that it works.

---

## Comment 5

> Username: sdebionne  
> Created at: 2019-10-25 15:38:09 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546403978  

I had a look at Boost.Iostreams that also have dependencies to third party libraries (zlib, bzip2) and it looks like it might have the same issue. I have open an issue boostorg/boost_install#18 (in parallel to your issue transfer).  
  
> It's not possible to use PUBLIC for an imported library, only INTERFACE linking is allowed, but I hope CMake does the right thing with those.  
  
I think it does the right thing.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-10-25 21:13:08 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546515389  

Looking at the documentation of FindMPI,  
  
* shouldn't the correct target be MPI::MPI_CXX?  
* seems that targets are only available in CMake 3.10, not sure whether we need to care about that though.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-10-25 22:34:06 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546534442  

Yes, `find_dependency(MPI)` finds just `MPI_CXX` for me by default, and does not declare `MPI::MPI_C`.

---

## Comment 8

> Username: pdimov  
> Created at: 2019-10-26 12:43:48 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546599512  

Almost done; the MPI tests just fail because of https://github.com/boostorg/mpi/commit/8619d7b24a7b6ff7d6655ee0b10ef7fb2a470480#commitcomment-35672247.

---

## Comment 9

> Username: pdimov  
> Created at: 2019-10-27 00:35:22 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546651361  

Merged to develop.

---

## Comment 10

> Username: sdebionne  
> Created at: 2019-10-28 08:10:57 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546836363  

@pdimov Thank you very much! I'll try to find some time to contribute a test.

---

## Comment 11

> Username: pdimov  
> Created at: 2019-10-28 12:40:39 UTC  
> Url: https://github.com/boostorg/boost_install/issues/17#issuecomment-546928232  

It already has a test: https://github.com/boostorg/boost_install/tree/develop/test/mpi
