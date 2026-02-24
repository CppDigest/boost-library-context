# #207 - Add support for replacing Boost.Fileystem with C++17 <filesystem> when available [Closed]

> Username: cmazakas  
> Created at: 2021-04-09 22:14:45 UTC  
> Updated at: 2022-05-19 09:00:18 UTC  
> Closed at: 2022-05-19 09:00:18 UTC  
> Url: https://github.com/boostorg/process/issues/207  

Boost.Process is a wonderful library but existed for longer than C++17 compilers have and as such, its API is polluted with `boost::filesystem::path`. This causes an awkward disconnect when being used in C++17 projects.  
  
This issue aims to start a discussion addressing this issue and the best path forward for giving C++17 users a way of using `std::filesytem::path`.

---

## Comment 1

> Username: jbgomond  
> Created at: 2021-05-24 17:25:31 UTC  
> Url: https://github.com/boostorg/process/issues/207#issuecomment-847209721  

+1 for with that one ! It seems that Boost DLL is using the flag BOOST_DLL_USE_STD_FS for backword compatibility with C++11.

---

## Comment 2

> Username: laanwj  
> Created at: 2022-01-13 11:38:57 UTC  
> Url: https://github.com/boostorg/process/issues/207#issuecomment-1012056944  

+1 to this, we ran into a similar issue

---

## Comment 3

> Username: ingo-loehken  
> Created at: 2022-03-29 13:41:38 UTC  
> Url: https://github.com/boostorg/process/issues/207#issuecomment-1081888231  

+1 for this. should be removed or be configurable like for boost.dll.
