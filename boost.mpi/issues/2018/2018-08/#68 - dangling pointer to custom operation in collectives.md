# #68 - dangling pointer to custom operation in collectives [Closed]

> Username: aminiussi  
> Created at: 2018-08-01 12:40:20 UTC  
> Updated at: 2018-08-02 12:50:33 UTC  
> Closed at: 2018-08-02 12:50:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/68  

Initially reported by @mkuron in https://github.com/boostorg/mpi/pull/52.  
  
I moving the PR into an issue, as the fix will be different than initially planed.

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-08-01 12:42:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/68#issuecomment-409561151  

Comment by @aminiussi  
  
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

> Username: aminiussi  
> Created at: 2018-08-01 12:43:04 UTC  
> Url: https://github.com/boostorg/mpi/issues/68#issuecomment-409561331  

Comment by @mkuron  
@aminiussi, thanks for the explanation. I have to admit that I only briefly looked at the Boost code and noticed I needed to added these `static`s to fix the variable lifetime issue that the compiler complained about.  
  
Stateless operations work correctly, but previously produced the warnings that my pull request silences. Stateful operations never really worked if I understand you correctly, so that would need to be documented. If your suggested change needed to enforce statelessness can't be made without breaking code, then I suppose documenting the lack of support for stateful operations is all we can do. We should still suppress these warnings for stateless operations though.

---

## Comment 3

> Username: aminiussi  
> Created at: 2018-08-01 12:43:40 UTC  
> Url: https://github.com/boostorg/mpi/issues/68#issuecomment-409561505  

by @aminiussi  
  
Documentation change in d7b35ef161a1059c0da6f6e6f88d76c4179bbf0f

---

## Comment 4

> Username: aminiussi  
> Created at: 2018-08-01 12:44:04 UTC  
> Url: https://github.com/boostorg/mpi/issues/68#issuecomment-409561603  

by @mkuron  
  
We still have the variable lifetime issue though that this pull request fixes; right now we are still calling member functions on a dangling pointer. This happens to work because the function doesn't access member variables, but is probably undefined behavior.  
  
I suppose we could do user_op<Op, T> mpi_op(Op()) to get rid of the static variables though.

---

## Comment 5

> Username: aminiussi  
> Created at: 2018-08-01 12:48:47 UTC  
> Url: https://github.com/boostorg/mpi/issues/68#issuecomment-409562884  

Branch [user-op-state-68](https://github.com/boostorg/mpi/tree/bugfix/user-op-state-68) has been created for this issue.
