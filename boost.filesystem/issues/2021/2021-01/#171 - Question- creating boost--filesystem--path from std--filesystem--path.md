# #171 - Question: creating boost::filesystem::path from std::filesystem::path [Closed]

> Username: daravi  
> Created at: 2021-01-05 18:40:42 UTC  
> Updated at: 2021-01-11 21:49:14 UTC  
> Closed at: 2021-01-11 21:49:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/171  

Hello, I was wondering if there is a technical reason that `boost::filesystem::path`'s constructor has not been updated to accept a `std::filesystem::path` as the argument? If this has not been considered before I would like to suggest adding this overload which would make it easier to use boost types that need a `boost::filesystem::path`.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-05 21:09:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/171#issuecomment-754900955  

The reason is that conversion between `boost::filesystem::path` and `std::filesystem::path` (in either direction) would require deep copying the path contents, which can be considered expensive. Making the conversion implicit could result in subtle performance issues. However, I'm open to discussion on the mailing list.  
  
There are also differences in behavior in some corner cases between Boost and std, so the conversion could result in surprises.

---

## Comment 2

> Username: daravi  
> Created at: 2021-01-11 21:49:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/171#issuecomment-758246913  

Ok I will follow up on the mailing list then. Thanks.
