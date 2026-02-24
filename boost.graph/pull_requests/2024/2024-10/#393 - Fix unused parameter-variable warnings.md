# #393 Fix unused parameter/variable warnings [Merged]

> Username: JulienDelacroix  
> Created at: 2024-10-31 16:29:32 UTC  
> Updated at: 2024-11-01 05:15:06 UTC  
> Merged at: 2024-11-01 05:14:55 UTC  
> Closed at: 2024-11-01 05:14:55 UTC  
> Url: https://github.com/boostorg/graph/pull/393  

Fix the following warnings:  
  
/data/mwrep/res/osp/Boost/23-0-0-4/include/boost/graph/bron_kerbosch_all_cliques.hpp:107:54: error: unused parameter 'g' [-Werror,-Wunused-parameter]  
  107 |     inline void clique(const Clique& p, const Graph& g)  
  
/data/mwrep/res/osp/Boost/23-0-0-4/include/boost/graph/bron_kerbosch_all_cliques.hpp:223:41: error: unused variable 'j' [-Werror,-Wunused-variable]  
  223 |         typename Container::iterator i, j;

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-11-01 05:15:05 UTC  
> Url: https://github.com/boostorg/graph/pull/393#issuecomment-2451308081  

Thank you!

---
