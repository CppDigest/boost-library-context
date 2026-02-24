# #458 - [FEATURE] Supporting undirected CSR graphs [Open]

> Username: Becheler  
> Created at: 2026-02-19 11:31:41 UTC  
> Updated at: 2026-02-23 09:20:33 UTC  
> Url: https://github.com/boostorg/graph/issues/458  

## Problem  
  
Currently, the Boost Graph Library's `compressed_sparse_row_graph` only supports directed and bidirectional graphs. There are explicit comments in the codebase and documentation indicating that undirected CSR support is a planned but unimplemented feature:  
  
- [`compressed_sparse_row_graph.hpp`](https://github.com/boostorg/graph/blob/3131c24630e42c79b43c1f32558041c219ab84b8/include/boost/graph/compressed_sparse_row_graph.hpp#L239-L259):  
  > At this time, the compressed sparse row graph can only be used to create directed and bidirectional graphs. In the future, undirected CSR graphs will also be supported.  
- [`doc/compressed_sparse_row.html`](https://github.com/boostorg/graph/blob/3131c24630e42c79b43c1f32558041c219ab84b8/doc/compressed_sparse_row.html#L316-L337):  
  > At this time, the CSR graph type only supports directed and bidirectional graphs...  
  
This is problematic for several reasons, e.g. for adjusting performance in my Louvain's implementation algorithm: I can't use Louvain on directed graphs, but I can't use undirected CSR. Consequently a gap exists between BGL implementations and GenLouvain (that uses CSR). Having an undirected CSR could bridge that gap:  
  
<img width="4170" height="1484" alt="Image" src="https://github.com/user-attachments/assets/d946f31c-23b0-420a-b37a-7d94d1296c11" />  
  
## Feature Request  
  
Add support for undirected compressed sparse row (CSR) graphs to `compressed_sparse_row_graph`. This would improve memory efficiency and performance for large undirected graphs, matching the benefits provided for directed graphs.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2026-02-23 09:20:33 UTC  
> Url: https://github.com/boostorg/graph/issues/458#issuecomment-3943558448  

This doesn't sound too hard in theory?
