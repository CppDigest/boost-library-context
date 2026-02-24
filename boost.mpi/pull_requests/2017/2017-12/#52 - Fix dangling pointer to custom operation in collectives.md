# #52 Fix dangling pointer to custom operation in collectives [Closed]

> Username: mkuron  
> Created at: 2017-12-20 13:59:24 UTC  
> Updated at: 2018-08-01 12:44:37 UTC  
> Closed at: 2018-08-01 12:44:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/52  

Found with Clang-Tidy in Boost 1.58.  
  
```  
/usr/include/boost/mpi/collectives/all_reduce.hpp:59:3: error: Address of stack memory associated with local variable 'op' is still referred to by the global variable 'op_ptr' upon returning to the caller.  This will be a dangling reference [clang-analyzer-core.StackAddressEscape,-warnings-as-errors]  
  }  
  ^  
main.cpp:54:3: note: Calling 'all_reduce'  
  boost::mpi::all_reduce(comm, value == root_value, is_same,  
  ^  
/usr/include/boost/mpi/collectives/all_reduce.hpp:115:3: note: Calling 'all_reduce_impl'  
  detail::all_reduce_impl(comm, &in_value, 1, &out_value, op,  
  ^  
/usr/include/boost/mpi/collectives/all_reduce.hpp:59:3: note: Address of stack memory associated with local variable 'op' is still referred to by the global variable 'op_ptr' upon returning to the caller.  This will be a dangling reference  
  }  
  ^  
```  
  
`user_op` takes a reference to `op` and `all_reduce_impl`/`reduce_impl`/`scan_impl` take it by value, so this seems to be the only place where this can be fixed.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-01-08 15:29:01 UTC  
> Url: https://github.com/boostorg/mpi/pull/52#issuecomment-355998048  

This is tricky, I realize I never looked into that corner...  
Basically, we're suggesting that we can provide statefull operation (hence the copy of the function object).  
  
But if we look the underlying C API of [MPI_Op_create](https://www.open-mpi.org/doc/v2.0/man3/MPI_Op_create.3.php), we see that this not supported:  
```  
typedef void MPI_User_function(void *invec, void *inoutvec,  
                                 int *len,  
                                 MPI_Datatype *datatype);  
```  
The root of the problem raised by that mismatch is actually in the `user_op`  object (in [operations.hpp](/boostorg/mpi/tree/develop/include/boost/mpi/operations.hpp#L292)). In order to compensate for that lack of state, we declare a `static Op* op_ptr;` that will bind to our statefull C++ function object.  
  
That is sooooo bad. If we do have a state in the function object, multi-threading will kill us: If we have two different states for the same couple `<Op,T>`, since they will share that static, the last call to 'user_op<Op,T>` will reset the state that will be used by all instances. (In the absence of multi-threading, the last one will be the good one, we should still reset `op_ptr` to 0 to make valgrind happy).  
  
I don't think you modification actually fixes the problem, it just make the compiler happy for no good reason.  
  
Right now, the only real solution I can think of would be to state clearly in the documentation that we require stateless functions objects (in line with the MPI requirements) and use a fresh instance of the **op** object in perform:  
```  
static void BOOST_MPI_CALLING_CONVENTION perform(void* vinvec, void* voutvec, int* plen, MPI_Datatype*)  
    {  
      T* invec = static_cast<T*>(vinvec);  
      T* outvec = static_cast<T*>(voutvec);  
      Op op; // independent instance  
      std::transform(invec, invec + *plen, outvec, outvec, op);  
    }  
```  
the `static Op* op_ptr;` would be removed.  
  
But that's a change that would break code that just happens to work so far.

---

## Comment 2

> Username: mkuron  
> Created_at: 2018-01-08 15:45:35 UTC  
> Url: https://github.com/boostorg/mpi/pull/52#issuecomment-356003300  

@aminiussi, thanks for the explanation. I have to admit that I only briefly looked at the Boost code and noticed I needed to added these `static`s to fix the variable lifetime issue that the compiler complained about.  
  
Stateless operations work correctly, but previously produced the warnings that my pull request silences. Stateful operations never really worked if I understand you correctly, so that would need to be documented. If your suggested change needed to enforce statelessness can't be made without breaking code, then I suppose documenting the lack of support for stateful operations is all we can do. We should still suppress these warnings for stateless operations though.

---

## Comment 3

> Username: aminiussi  
> Created_at: 2018-07-31 09:40:48 UTC  
> Url: https://github.com/boostorg/mpi/pull/52#issuecomment-409160570  

Documentation change in d7b35ef161a1059c0da6f6e6f88d76c4179bbf0f

---

## Comment 4

> Username: mkuron  
> Created_at: 2018-07-31 16:14:01 UTC  
> Updated_at: 2018-07-31 16:16:07 UTC  
> Url: https://github.com/boostorg/mpi/pull/52#issuecomment-409278709  

We still have the variable lifetime issue though that this pull request fixes; right now we are still calling member functions on a dangling pointer. This happens to work because the function doesn't access member variables, but is probably undefined behavior.  
  
I suppose we could do `user_op<Op, T> mpi_op(Op())` to get rid of the static variables though.

---

## Comment 5

> Username: aminiussi  
> Created_at: 2018-08-01 12:44:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/52#issuecomment-409561751  

Moving discussion to #68

---
