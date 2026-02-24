# #55 Add MPI error code to string convertion [Merged]

> Username: aminiussi  
> Created at: 2018-03-13 16:53:45 UTC  
> Updated at: 2018-04-18 14:12:20 UTC  
> Merged at: 2018-04-18 14:12:10 UTC  
> Closed at: 2018-04-18 14:12:10 UTC  
> Url: https://github.com/boostorg/mpi/pull/55  

We are missing a clean conversion from MPI error code to strings.  
This is a C++ version of MPI_Error_string

---

## Review 1 [Approved]

> Username: Belcourt  
> Created_at: 2018-04-09 22:16:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/mpi/pull/55#pullrequestreview-110636380  

Style comment, keep the function declaration and return type on the same line unless they're too long.  That is, the header declaration of error_string is preferable to the source file definition.

---

## Comment 2

> Username: drewxa  
> Created_at: 2018-04-16 16:42:48 UTC  
> Url: https://github.com/boostorg/mpi/pull/55#issuecomment-381670115  

Perhaps the function `error_string` should be used [here](https://github.com/boostorg/mpi/blob/develop/src/exception.cpp#L19)?

---

## Comment 3

> Username: aminiussi  
> Created_at: 2018-04-17 08:04:42 UTC  
> Updated_at: 2018-04-18 14:09:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/55#issuecomment-381889657  

Yes, you're right.  
It's been pushed.

---
