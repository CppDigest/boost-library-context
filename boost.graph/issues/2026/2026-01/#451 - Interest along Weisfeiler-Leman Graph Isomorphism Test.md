# #451 - Interest along Weisfeiler-Leman Graph Isomorphism Test [Open]

> Username: Becheler  
> Created at: 2026-01-30 09:49:03 UTC  
> Updated at: 2026-02-17 13:00:24 UTC  
> Url: https://github.com/boostorg/graph/issues/451  

Hi Graphies,  
  
I received a feature request from a graph scientist: the Weisfeiler-Leman (WL) graph isomorphism test, a popular heuristic for detecting non-isomorphic graphs. From what I understand, WL quickly proves non-isomorphism in polynomial time, so one can skip exponential exact algorithm most of the time. So it should problably complement existing BGL algorothms like:  
- `isomorphism()` Exact algorithm, worst-case exponential time  
- `vf2_sub_graph_iso()` Subgraph matching, even slower  
  
## Literature  
- Founding article: [Weisfeiler & Leman, 1968](https://iti.zcu.cz/wl2018/pdf/wl_paper_translation.pdf)  
- Power and Limits: [Kiefer 2020](https://publications.rwth-aachen.de/record/785831/files/785831.pdf)  
  
What do you think ?

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2026-02-17 12:59:24 UTC  
> Url: https://github.com/boostorg/graph/issues/451#issuecomment-3914575346  

Sorry, somehow I missed this when you posted it.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2026-02-17 13:00:24 UTC  
> Url: https://github.com/boostorg/graph/issues/451#issuecomment-3914579788  

But in general, yes, fast polynomial approximations/heuristics to avoid exact exponential algorithms are a great idea.
