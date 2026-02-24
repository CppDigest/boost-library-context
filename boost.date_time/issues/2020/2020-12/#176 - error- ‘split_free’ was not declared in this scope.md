# #176 - error: ‘split_free’ was not declared in this scope [Closed]

> Username: neel  
> Created at: 2020-12-12 12:54:59 UTC  
> Updated at: 2020-12-30 10:36:59 UTC  
> Closed at: 2020-12-30 10:36:59 UTC  
> Url: https://github.com/boostorg/date_time/issues/176  

After updating to Boost 1.74 I am getting too many warning messages  
  
> /usr/include/boost/function_output_iterator.hpp:14:1: note: ‘#pragma message: This header is deprecated. Use <boost/iterator/function_output_iterator.hpp> instead.’  
  
Along with a compilation error.  
  
> /usr/include/boost/date_time/posix_time/time_serialize.hpp:48:1: error: ‘split_free’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
  
I understand that these warnings are unrelated with the error. But can I do anything to suppress these warnings ? Is this error a bug to be fixed ? or I need an workaround ?  
  
I am using C++14. Everything was working fine till Boost 1.72

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-12-12 23:00:06 UTC  
> Url: https://github.com/boostorg/date_time/issues/176#issuecomment-743917126  

Sorry which compiler/version/platform is this?  Also could you provide in particular the boost headers being included and what order they are in?

---

## Comment 2

> Username: neel  
> Created at: 2020-12-13 12:06:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/176#issuecomment-743997933  

Applies to all compilers I suppose. I am using gcc 10 on arch linux.  
  
workaround is to change order of header.  
  
https://stackoverflow.com/questions/65270182/boost-1-74-serializing-boostposix-timeptime-getting-error-split-free-was/65270905#65270905

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-12-13 16:23:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/176#issuecomment-744031890  

So yes, that was a change from a couple release back I think that intentionally avoids including serialization headers -- which were causing other issues as I recall.  If you look at the time_serialize header you'll see the note below - it was very much intentional -- the fact that it induces an include ordering requirement on the user is unfortunate.   
  
```  
// A macro to split serialize functions into save & load functions.  
// It is here to avoid dependency on Boost.Serialization just for the  
// BOOST_SERIALIZATION_SPLIT_FREE macro  
#define BOOST_DATE_TIME_SPLIT_FREE(T)                                         \  
template<class Archive>                                                       \  
inline void serialize(Archive & ar,                                           \  
                      T & t,                                                  \  
                      const unsigned int file_version)                        \  
{                                                                             \  
  split_free(ar, t, file_version);                                            \  
   
  
```  
https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/posix_time/time_serialize.hpp  
  
Anyway, the warning is still an issue that I'll look into.
