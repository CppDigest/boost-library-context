# #10 Fix construction of any from static_binding. [Closed]

> Username: DenizThatMenace  
> Created at: 2016-06-22 11:05:58 UTC  
> Updated at: 2017-03-24 15:17:49 UTC  
> Closed at: 2017-03-24 15:17:49 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/10  

Constructing an any from a static_binding resulted in the wrong overload being chosen (when rvalue-references are supported).  
This pull-request fixes that.  
  
Additionally, some compile- and unit-tests regarding `binding` and `binding_of` were added and extended.  
  
The associated trac-ticket is: [12292](https://svn.boost.org/trac/boost/ticket/12292)

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-03-24 15:17:45 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/10#issuecomment-289052248  

As I noted in the trac ticket, I don't consider this correct.  
  
The error in check_match.hpp is a legitimate bug.  This use is already tested in test_construct.cpp, but it didn't use relaxed.  I adjusted the construction tests to run both with and without relaxed.

---
