# #34 Document that metis_reader expects a seekable stream [Merged]

> Username: J5lx  
> Created at: 2022-02-19 22:38:47 UTC  
> Updated at: 2025-09-24 09:54:05 UTC  
> Merged at: 2025-09-24 09:54:05 UTC  
> Closed at: 2025-09-24 09:54:05 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/34  

metis_reader unconditionally seeks to the beginning of the stream when it starts, which causes it to fail with a metis_input_exception when reading from streams that are not seekable. For example, in my case I’m trying to read a graph that is piped into my program via stdin, which does not work because of this. This PR adds a note about that to the documentation to prevent surprises.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-09-24 09:53:43 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/34#issuecomment-3327520500  

Do you still use `graph_parallel`?

---
