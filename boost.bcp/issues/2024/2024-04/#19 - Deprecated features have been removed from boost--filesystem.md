# #19 - Deprecated features have been removed from boost::filesystem. [Closed]

> Username: syntax-case  
> Created at: 2024-04-18 07:51:50 UTC  
> Updated at: 2024-08-15 05:49:56 UTC  
> Closed at: 2024-08-15 05:49:56 UTC  
> Url: https://github.com/boostorg/bcp/issues/19  

bcp uses a couple of features that have been removed from boost::filesystem in the 1.85.0 release of boost.   
See https://www.boost.org/doc/libs/1_85_0/libs/filesystem/doc/deprecated.html   
As a result bcp can't be built anymore using the latest version.  
It looks like most of the issues are functions that had their name changed, or definitions that moved to a different header.   
  
This was fixed on the develop branch, but master hasn't been updated yet.
