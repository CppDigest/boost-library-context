# #17 classes with virtual function must have virtual destructors. [Merged]

> Username: jhunold  
> Created at: 2015-06-15 08:50:50 UTC  
> Updated at: 2015-09-29 06:56:45 UTC  
> Merged at: 2015-09-29 06:56:45 UTC  
> Closed at: 2015-09-29 06:56:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/17  

The warning is not "bogus" and not only gcc warns.  
clang will warn too, but defines **GNUC** for gcc compatibility.  
msvc issues "warning C4265: 'boost::serialization::<foo>' :  
class has virtual functions, but destructor is not virtual"  
The msvc warning could be suppressed, but it is much cleaner to simply  
make the destructors virtual as they are with gcc and clang.

---
