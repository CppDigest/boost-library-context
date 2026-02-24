# #231 - foo_oarchive methods to allow writing in earlier formats [Closed]

> Username: UnitedMarsupials  
> Created at: 2021-04-03 21:10:59 UTC  
> Updated at: 2022-02-24 22:45:05 UTC  
> Closed at: 2022-02-24 22:45:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/231  

We use `boost::archive::binary_iarchive` and `binary_oarchive` here, and run into the following issue once in a while: a developer upgrades his local Boost-version and now the data-blobs produced by his program are not readable by others, forcing them to upgrade too, often at an inopportunate time.  
  
I gather, the problem is not specific to the binary methods, but that text- and XML-formats have it too.  
  
There has not been a problem in the other direction: de-serialization of data written by older Boost-versions works in newer version. Which means, the deserializer (`foo_iarchive`) is backwards compatible.  
  
It'd be a big improvement, if serializer was also so compatible: if it were possible to request -- perhaps, through the `flags` argument for all the `foo_oarchive`-methods -- that the saved file be in a format understood by an _earlier_ version of deserializer. Going back for, at least, one -- preferably, more -- versions.  
  
See also [this StackOverflow question](https://stackoverflow.com/questions/66877005/).

---

## Comment 1

> Username: robertramey  
> Created at: 2022-02-24 22:45:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/231#issuecomment-1050335421  

this has been suggested before.  I've rejected the suggestion as being too much work for the benefit received.
