# #103 Fix friend declarations for iterator_core_access [Merged]

> Username: smeenai  
> Created at: 2018-02-28 23:45:59 UTC  
> Updated at: 2018-03-01 20:24:45 UTC  
> Merged at: 2018-03-01 20:24:45 UTC  
> Closed at: 2018-03-01 20:24:45 UTC  
> Url: https://github.com/boostorg/graph/pull/103  

csr_out_edge_iterator and csr_in_edge_iterator are part of the  
boost::detail namespace, so they need to explicitly qualify  
iterator_core_access in their friend declarations. Note that  
csr_edge_iterator already gets this correct.

---
