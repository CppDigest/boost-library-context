# #22 Add a basic sendrecv that should be a direct transcription of the C interface [Merged]

> Username: aminiussi  
> Created at: 2014-10-28 13:56:27 UTC  
> Updated at: 2014-10-28 23:50:46 UTC  
> Merged at: 2014-10-28 23:50:42 UTC  
> Closed at: 2014-10-28 23:50:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/22  

SendRecv is probably mostly relevant when used with Cartesian communicators. Still, it's probably simpler/cleaner to introduce them separately.  
This is a simple translation of the C interface for the "simple" types and a async send/recv pair followed by a wait for more complex types.

---
