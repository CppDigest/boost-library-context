# #65 - Python crash on deleting shared_ptrs stored in a C++ vector [Open]

> Username: prav076  
> Created at: 2016-04-29 03:33:48 UTC  
> Updated at: 2016-04-29 03:34:23 UTC  
> Url: https://github.com/boostorg/python/issues/65  

Hi,  
  
 There is a struct A defined in C++ and exposed to python as shared_ptr.  
 There are two global vectors of shared_ptrs of A.  
 Create and add a object of type A from python to C++ in the two vectors  
 Clear one vector  
 Then when the python exits the program crashes with access violation  
 error, while deleting the shared_ptrs  
  
[TestShared.zip](https://github.com/boostorg/python/files/241747/TestShared.zip)  
  
Environment: Boost 1.58.0, compiler VS 2012  
  
 I have attached both C++ and python file to recreate this issue.  
  
 Thanks
