# #347 - [Feature request] Implementation of Held-Karp lower bound for the TSP [Open]

> Username: TryerGit  
> Created at: 2023-08-31 13:30:38 UTC  
> Updated at: 2023-09-01 00:59:00 UTC  
> Url: https://github.com/boostorg/graph/issues/347  

The Held-Karp lower bound for the TSP is a good lower bound for the TSP and at a big picture, it attempts to approximate the TSP from below by trying to obtain trees where a large number of nodes have degree 2. It still continues to attract research interest in the OR community. See for e.g., https://ojmo.centre-mersenne.org/item/10.5802/ojmo.11.pdf a recent work on this.  
  
Google OR-Tools seems to provide an interface for this. Please see https://developers.google.com/optimization/reference/graph/one_tree_lower_bound  
  
It would be great if the BGL team considers implementing the same in its libraries. I use BGL for many MST algorithms and it is very efficient. The Held-Karp lower bound uses MST as a subproblem.  
  
Thank you.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2023-09-01 00:59:00 UTC  
> Url: https://github.com/boostorg/graph/issues/347#issuecomment-1701966326  

Anyone can be a member of the BGL team, we are an open and inclusive community!  
  
If you want it, you're probably the best person to build it.
