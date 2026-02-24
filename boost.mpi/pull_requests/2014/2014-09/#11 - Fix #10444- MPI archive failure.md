# #11 Fix #10444: MPI archive failure [Merged]

> Username: ventrebd  
> Created at: 2014-09-04 13:15:06 UTC  
> Updated at: 2014-12-23 16:41:17 UTC  
> Merged at: 2014-10-31 20:37:41 UTC  
> Closed at: 2014-10-31 20:37:41 UTC  
> Url: https://github.com/boostorg/mpi/pull/11  

Add "const" to save_override for archive::class_id_type and archive::version_type.  WIthout it, they do not participate in overload resolution when called with a const argument, and the default template function is called.  This leads to saving 4 bytes for archive::version_type, but only loading 1 -- all subsequent load operations are then off by 3 bytes.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-09-07 23:03:17 UTC  
> Url: https://github.com/boostorg/mpi/pull/11#issuecomment-54764141  

I'm not able to reproduce the error you describe, though I'm okay with the change.  What platform does this occur on (toolset, build options, etc...)?

---

## Comment 2

> Username: ventrebd  
> Created_at: 2014-09-08 13:11:04 UTC  
> Url: https://github.com/boostorg/mpi/pull/11#issuecomment-54814673  

I use MS Visual Studio 2012 update 4 on Windows x64.  Also, I always enable BOOST_MPI_HOMOGENEOUS (which might be the root cause).  Fixing these two lines in 1.56.0 & rebuilding boost solved the issue on my end.  
  
The calls to these functions occur from within the serialization library (basic_oarchive.cpp, line 264 to save; basic_iarchive.cpp, line 336 to load).  
  
More details at:  https://svn.boost.org/trac/boost/ticket/10444

---

## Comment 3

> Username: ventrebd  
> Created_at: 2014-10-22 13:16:18 UTC  
> Url: https://github.com/boostorg/mpi/pull/11#issuecomment-60082386  

Can this be merged in time for the 1.57 release?  Thanks.

---
