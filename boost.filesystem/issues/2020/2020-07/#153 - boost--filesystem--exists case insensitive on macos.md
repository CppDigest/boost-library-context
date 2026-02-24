# #153 - boost::filesystem::exists case insensitive on macos [Closed]

> Username: zgpio  
> Created at: 2020-07-08 10:52:03 UTC  
> Updated at: 2020-07-08 11:19:33 UTC  
> Closed at: 2020-07-08 11:19:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/153  

version: boost 1.70.0  
system: macos 10.14  
  
Expected behavior: boost::filesystem::exists should be case sensitive  
  
test case: "fool.txt", "foOl.txt"

---

## Comment 1

> Username: Lastique  
> Created at: 2020-07-08 11:19:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/153#issuecomment-655455471  

Boost.Filesystem does not perform case conversion and uses `stat` system call to implement `status` (and, consequently, `exists`). If the test says the file exists then that is what the underlying filesystem reports.
