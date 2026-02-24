# #448 - Any interest in Nearest Neighbor greedy tour algorithm and/or cleaned up metric_tsp_approx? [Open]

> Username: megyhazy  
> Created at: 2026-01-11 02:43:47 UTC  
> Updated at: 2026-01-15 03:15:53 UTC  
> Url: https://github.com/boostorg/graph/issues/448  

Hi,  
  
I have written a Nearest Neighbor greedy tour heuristic w/ example and test code.  I think this heuristic is a good pairing to the metric_tsp as it has less constraints on the graph/edge weight types (no triangle inequality required), does not require a global MST, uses less memory while running due to local greedy characteristic, and runs much faster albeit with the caveat that it does not have a defined upper bound on tour efficiency.  However, typical results are very strong and it is a good starting point to the more advanced k-opt TSP algorithms (e.g. LKH) that require a starting tour.  
  
```  
 template < typename VertexListGraph, typename WeightMap,  
     typename VertexIndexMap, typename TSPVertexVisitor >  
 void nearest_neighbor_tour_from_vertex(const VertexListGraph& g,  
     typename graph_traits< VertexListGraph >::vertex_descriptor start,  
     WeightMap weightmap,  
     VertexIndexMap indexmap,  
     TSPVertexVisitor vis)  
  
```  
  
I also have cleaned up my original tsp_metric_approx code significantly, addressing all of Andrew Sutton's (who helped tremendously) original comments.  I've also improved the test and example code for it.  
  
I am also considering implementing Christofides 3/2 solution O(v^3) due to MWPM because it has the interesting characteristic of not requiring a complete graph as well as a lower upper bound solution, but I wanted to write a fast starter for LK first.     
  
Let me know if there is any interest in either of these (cleaned up metric, new nearest neighbor).  I haven't contributed to Boost BGL in nearly 20 years and I know much has changed, but I had interest over the holidays :-)

---

## Comment 1

> Username: Becheler  
> Created at: 2026-01-12 14:21:22 UTC  
> Updated at: 2026-01-12 15:16:06 UTC  
> Url: https://github.com/boostorg/graph/issues/448#issuecomment-3738794010  

Wahooo Welcome Back @megyhazy ! 🥳   
  
I am quite new here but it looks like a great addition! Even more if it paves the way to more advanced algorithms.  
  
I am not sure how you prefer to seperate concerns (one PR for cleaned up metric, another for new nearest neighbor, or both combined?), but I will be happy to help where I can, including testing on graphs, documentation or benchmarks. Just ask !  
  
So happy you're back after 20 years Matyas ! 🤩   
  
PS: I don't know, but are you on Slack by any chance: https://cppalliance.org/slack/

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2026-01-14 22:48:42 UTC  
> Url: https://github.com/boostorg/graph/issues/448#issuecomment-3752054577  

Hi @megyhazy , welcome back! I'm the current maintainer, and I have to say, it feels like quite an honour and a testament to the library's quality to have a contributor return after 20 years!  
  
And it's funny, but just this morning I was thinking about how to make the library feel fresh again, and adding some new algorithms was right up there!   
  
Fixing bugs is my top priority, but I'm very excited by the prospect of some new algorithms from an experienced contributor. I'm quite opinionated about interface design, so please be prepared for that.  :)  
  
Actually, I'm not sure if that much has changed, really! I recently bumped the permitted standard to C++14. I'm not a fan of `bgl_named_params` and discourage the use of it, btw, not sure if you have strong feelings about that?  
  
Otherwise, please go to town and I will do my best to respond quickly.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2026-01-14 22:50:07 UTC  
> Url: https://github.com/boostorg/graph/issues/448#issuecomment-3752061003  

Oh, one other thing, since you have multiple good ideas, I will add that I definitely prefer one idea per pull request, please.  :)

---

## Comment 4

> Username: megyhazy  
> Created at: 2026-01-15 03:15:53 UTC  
> Url: https://github.com/boostorg/graph/issues/448#issuecomment-3752714263  

Thanks!  I will collaborate with Arnaud and work with Git after that.  
Probably needs two separate submissions as you say (1 for metric tsp  
cleanup and 2 for nearest neighbor) - but there are dependencies due to  
moving some tsp, tree, and graph utilities into shared headers.  
  
Sent from Gmail Mobile, please forgive typos.  
  
  
On Wed, Jan 14, 2026 at 5:50 PM Jeremy W. Murphy ***@***.***>  
wrote:  
  
> *jeremy-murphy* left a comment (boostorg/graph#448)  
> <https://github.com/boostorg/graph/issues/448#issuecomment-3752061003>  
>  
> Oh, one other thing, since you have multiple good ideas, I will add that I  
> definitely prefer one idea per pull request, please. :)  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/issues/448#issuecomment-3752061003>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/BTCHAOBVP47QBBWALIU6UPT4G3B3JAVCNFSM6AAAAACRJZ2WDCVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZTONJSGA3DCMBQGM>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>
