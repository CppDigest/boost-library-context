# #39 - Expect either boost::optional or std::optional from Boost.MPI [Open]

> Username: aminiussi  
> Created at: 2025-05-06 09:50:41 UTC  
> Updated at: 2025-10-14 11:12:00 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/39  

Hi,  
  
Boost.MPI is moving from boost::optional to std::optional when the later is available (unless explicitly asked otherwise).  
During the transition phase, clients can expect either, which implies to   
 * only rely on the common API between `boost::optional` and `std::optional`  
 * take the Boost MPI type from `boost::mpi::optional`  
  
I'll submit a pull request  with those modifications.

---

## Comment 1

> Username: aminiussi  
> Created at: 2025-05-06 11:45:31 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/39#issuecomment-2854271490  

The pull request https://github.com/boostorg/graph_parallel/pull/40 implement that change.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2025-10-13 23:08:27 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/39#issuecomment-3399355432  

@aminiussi  , if you still use this library, it is in desperate need of help to fix existing infrastructure problems, so I am calling on all users to please help if they can.

---

## Comment 3

> Username: aminiussi  
> Created at: 2025-10-14 11:12:00 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/39#issuecomment-3401285835  

@jeremy-murphy I do not use graph_parallel (and never did if I remember correctly). I think graph parallel uses Boost.mpi, thus the dependency.   
I can help if something need to be done in Boost mpi to make graph_parallel life easy.
