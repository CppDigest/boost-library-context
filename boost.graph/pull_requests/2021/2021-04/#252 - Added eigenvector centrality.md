# #252 Added eigenvector centrality [Open]

> Username: Amritha16  
> Created at: 2021-04-20 11:02:48 UTC  
> Updated at: 2021-05-30 17:53:52 UTC  
> Url: https://github.com/boostorg/graph/pull/252  



---

## Comment 1

> Username: Valliammai19  
> Created_at: 2021-04-20 13:38:49 UTC  
> Url: https://github.com/boostorg/graph/pull/252#issuecomment-823281981  

Eigenvector centrality is a useful centrality measure. Although Boost has its directed variant, Pagerank @Amritha16 and I couldn't find an implementation of Eigenvector centrality

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2021-05-05 23:58:34 UTC  
> Updated_at: 2021-05-05 23:58:55 UTC  
> Url: https://github.com/boostorg/graph/pull/252#issuecomment-833123474  

Thanks for contributing this idea. The two algorithms look almost identical. Rather than duplicate the code with a minor change, I think it would be much better to rename the `page_rank` code to a general name, node_rank or whatever, parameterize it so that then `page_rank` and `eigenvector_centrality` just use `node_rank`  with a functor that calculates that `u_rank_out`.  
How does that sound?  
  
Some unit tests wouldn't go astray either.  ;)

---

## Comment 3

> Username: Amritha16  
> Created_at: 2021-05-30 17:53:52 UTC  
> Url: https://github.com/boostorg/graph/pull/252#issuecomment-851036996  

@jeremy-murphy That sounds much better than our initial commit. Thanks for the suggestion!:)  
@Valliammai19 and I will get on this next!

---
