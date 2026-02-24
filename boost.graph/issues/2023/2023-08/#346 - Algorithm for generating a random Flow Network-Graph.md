# #346 - Algorithm for generating a random Flow Network/Graph [Open]

> Username: Bashar-Ahmed  
> Created at: 2023-08-30 19:33:02 UTC  
> Updated at: 2023-09-01 00:53:53 UTC  
> Url: https://github.com/boostorg/graph/issues/346  

Dear all,  
  
Recently, I was working on a Flow Decomposition Problem, and found out that there are not many datasets for large valid flow networks. Nor could I find any existing software to generate such a Network/Graph. Are there any plans to develop such an algorithm within Boost. If yes, I would definitely like to contribute towards it.  
  
Basically, by a valid flow network, I mean that there will be certain vertices as sources with `input_flow = 0` , certain vertices as sinks `output_flow = 0` , and rest all vertices should follow conservation of flow, i.e., `input_flow = output_flow`  
  
Thank you,  
Bashar Ahmed.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2023-09-01 00:53:53 UTC  
> Url: https://github.com/boostorg/graph/issues/346#issuecomment-1701963041  

There are no plans at the moment, sorry. If it interests you or is important to you, then you are probably the best person to do it.  :)  
  
There are several flow-related algorithms and there is an interface to generate random graphs, so it might be halfway there.
