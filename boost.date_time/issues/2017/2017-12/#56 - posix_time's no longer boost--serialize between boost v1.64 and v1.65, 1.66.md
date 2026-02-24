# #56 - posix_time's no longer boost::serialize between boost v1.64 and v1.65, 1.66 [Closed]

> Username: jdmairs  
> Created at: 2017-12-22 16:41:42 UTC  
> Updated at: 2017-12-30 21:26:43 UTC  
> Closed at: 2017-12-30 21:26:43 UTC  
> Url: https://github.com/boostorg/date_time/issues/56  

It appears that   
  
[4543: fix ptime 2038 issue ](https://github.com/boostorg/date_time/commit/818dea52f3f0c8b4f5172df13c5fa4f57340625f#diff-c68588b074fe6450f9ed1dedc759621e)  
  
has changed the behavior of boost::serialization.  My archives (built with boost 1.64) using boost::serialization (inherently using time_serialization.hpp) no longer load because the hours, minutes, etc fields are now 8 bytes instead of 4 bytes.  
  
I'm using VS2017 15.4 and 15.2

---

## Comment 1

> Username: jeking3  
> Created at: 2017-12-22 21:36:41 UTC  
> Url: https://github.com/boostorg/date_time/issues/56#issuecomment-353678294  

Yes, this is caused by #35.  I'm looking into it.  Boost.Serialization has versioning capabilities that could allow for compatibility, except that 1.65 an 1.66 will be writing the same version, so it's now "a mess".
