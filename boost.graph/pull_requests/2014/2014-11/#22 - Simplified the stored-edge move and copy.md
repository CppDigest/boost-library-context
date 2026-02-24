# #22 Simplified the stored-edge move and copy [Merged]

> Username: mikael-s-persson  
> Created at: 2014-11-06 06:37:55 UTC  
> Updated at: 2014-11-16 22:33:46 UTC  
> Merged at: 2014-11-16 22:33:46 UTC  
> Closed at: 2014-11-16 22:33:46 UTC  
> Url: https://github.com/boostorg/graph/pull/22  

Simplified the move and copy constructor and assignment to avoid the maintenance nightmare of trying to rely on default functions.  
  
This pull request is an alternative solution to a [previous pull-request](https://github.com/boostorg/graph/pull/18) in response to some additional issues raised on the [bug-tracker 10382](https://svn.boost.org/trac/boost/ticket/10382).  
  
The idea here is to avoid the reliance on default copy/move constructors and assignment operators. Dealing with various compiler behaviours when it comes to defaulting and deleting the implicit special functions is proving to be a maintenance nightmare. Since the stored_edge_property class contains a smart-pointer (auto_ptr for legacy and unique_ptr for C++11), there is really no point in relying on default special functions because that class will never be "trivial" (due to smart pointers not being "trivial"). By always providing those functions explicitly (user-defined), there is no more need for switches based on the compiler vendor and version that have different (right or wrong) behaviour for the defaulting / deleting of those functions. The only switch required now is for testing the presence of rvalue-ref and of C++11 smart-pointers.  
  
This pull request also fixes the "SGI MIPSpro" issue the correct way, by using `static_cast<Base&>(*this) = static_cast< Base const& >(x);` instead of using `Base::operator=(x);` (which requires a user-defined copy-assignment operator in the `stored_edge` class, which is where all the trouble of bug-10382 begins).

---
