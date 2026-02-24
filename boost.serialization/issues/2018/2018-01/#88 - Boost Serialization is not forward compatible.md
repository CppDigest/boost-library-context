# #88 - Boost Serialization is not forward compatible [Closed]

> Username: bilbothebaggins  
> Created at: 2018-01-24 10:59:33 UTC  
> Updated at: 2018-04-30 20:12:16 UTC  
> Closed at: 2018-04-30 20:12:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/88  

We have just learned, that boost serialization *text* archives are not forward compatible - that is, it is generally *not* possible to de-serialize text-archives from a newer boost version with an older one.  
  
The documentation calls this out in the exceptions description at [unsupported_version](http://www.boost.org/doc/libs/1_66_0/libs/serialization/doc/exceptions.html#unsupported_version), and also in the [ToDo](http://www.boost.org/doc/libs/1_66_0/libs/serialization/doc/todo.html) list ("Back Versioning"), but fails to mention it in any conceptual/introduction/feature part.  
  
I have created this issue:  
* To ask whether this could be called out more explicitly in the docs.  
* To highlight that the version in `basic_archive.cpp` seems to be constantly updated, namely we used to use v1.44 and that's version 7, while the current version in 1.66 is v16 - but even with Boost v1.62, that is not even 1,5 years old, the version has been upped twice from 14 to 16, where given the comments, I'm not even sure they created any incompatibility(??)  
* To ask whether there is any workaround (for *text* archives) to allow old versions reading newer archives, or whether we should just abandon Boost.Serialization for tasks that require different application to talk to each other. (Because we cannot keep the boost version here in sync currently.)  
  
Thanks!

---

## Comment 1

> Username: robertramey  
> Created at: 2018-04-30 20:12:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/88#issuecomment-385514436  

Generally, later versions of the library are guaranteed to be able to read older archives.  
  
"unsupported version" refers to an entirely different thing.   The ability of  a serialize function which supports class version up to version N to read newer classes of version type N + 1.  This is not supported.  Note that this is not related to the "boost version" - it only refers to the class version which is a feature of one's program.  
  
From time to time, someone requests the implementation of "forward compatibility" which would implement the above feature.  Though technically feasible, it would be very time consuming to implement.  There is insufficient demand for this feature ... and it's not important enough for anyone to offer to pay for it.
