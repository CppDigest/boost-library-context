# #653 refactor: Renamed io/dynamic_io_new.hpp to io/detail/dynamic.hpp [Merged]

> Username: marco-langer  
> Created at: 2022-04-26 21:13:45 UTC  
> Updated at: 2022-05-13 09:46:29 UTC  
> Merged at: 2022-04-27 11:27:13 UTC  
> Closed at: 2022-04-27 11:27:13 UTC  
> Url: https://github.com/boostorg/gil/pull/653  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Renamed `io/dynamic_io_new.hpp` to `io/detail/dynamic.hpp`.   
  
The function `construct_matched` was only used by other detail headers, thus I moved the function into the detail namespace as already suggested in the issue. Additionally I moved the whole file into the detail directory, because it only contains details now.  
  
  
### References  
  
Fixes #189  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-04-27 11:26:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/653#pullrequestreview-954737406  

LGTM, thank you!

---

## Comment 2

> Username: simmplecoder  
> Created_at: 2022-05-13 08:44:52 UTC  
> Updated_at: 2022-05-13 08:46:06 UTC  
> Url: https://github.com/boostorg/gil/pull/653#issuecomment-1125804514  

I guess I am a bit late, but this commit seems to be the one breaking the build for me. In all of the `write.hpp` headers of I/O extensions, `dynamic.hpp` is missing and that causes build error.  
Without including `dynamic.hpp`, the build will complain that `dynamic_io_fnobj` is not present in `detail` namespace. Including the header fixes the problem.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2022-05-13 09:46:28 UTC  
> Url: https://github.com/boostorg/gil/pull/653#issuecomment-1125859768  

`dynamic_io_new.hpp` was indeed renamed/moved in the include list of `read.hpp` while it was completely removed from `write.hpp`.

---
