# #40 Expect either boost::optional or std::optional from Boost.MPI [Open]

> Username: aminiussi  
> Created at: 2025-05-06 11:44:44 UTC  
> Updated at: 2025-05-13 09:16:33 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/40  

This is a 'fix' for https://github.com/boostorg/graph_parallel/issues/39.   
  
It assume Boost MPI develop version https://github.com/boostorg/mpi/commit/2eed4e7c7ac37c335f1c5175e6c7fc3f9b4e7347

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-05-13 09:13:06 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/40#issuecomment-2875668179  

This change looks fine but I'll have to fix whatever is wrong with the CI first, which I'm hoping it's just the Ubuntu 20.04 removal.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-05-13 09:16:33 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/40#issuecomment-2875678226  

> This change looks fine but I'll have to fix whatever is wrong with the CI first, which I'm hoping it's just the Ubuntu 20.04 removal.  
  
Hmm, no, it's something else, so this might take time to fix.

---
