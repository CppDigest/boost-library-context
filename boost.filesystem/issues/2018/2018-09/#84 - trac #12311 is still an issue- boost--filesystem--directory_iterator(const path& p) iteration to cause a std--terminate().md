# #84 - trac #12311 is still an issue: boost::filesystem::directory_iterator(const path& p) iteration to cause a std::terminate() [Closed]

> Username: zowers  
> Created at: 2018-09-11 10:02:35 UTC  
> Updated at: 2018-11-24 18:01:03 UTC  
> Closed at: 2018-11-24 18:01:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/84  

the Track issue https://svn.boost.org/trac10/ticket/12311 is still present, see https://github.com/boostorg/filesystem/blob/16821f7903f02d0bb6bb2ef2503ea41559c6bdf1/include/boost/filesystem/operations.hpp#L913  
  
`filesystem::directory_iterator::increment()` should not be `noexecpt`, see https://en.cppreference.com/w/cpp/filesystem/directory_iterator/increment

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-24 18:01:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/84#issuecomment-441384964  

The function is not marked as `noexcept` in the standard, but it can still be `noexcept` in Boost.Filesystem. I don't see a problem with this discrepancy.  
  
As for the Trac ticket, I believe, the problems mentioned there should be fixed as of 8c9bba511cce978c128b22c8d2fbfa5ab9ba9d99.
