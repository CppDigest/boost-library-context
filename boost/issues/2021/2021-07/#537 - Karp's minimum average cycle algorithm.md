# #537 - Karp's minimum average cycle algorithm [Closed]

> Username: ggleizer  
> Created at: 2021-07-19 08:21:27 UTC  
> Updated at: 2021-07-19 08:25:00 UTC  
> Closed at: 2021-07-19 08:25:00 UTC  
> Url: https://github.com/boostorg/boost/issues/537  

Dear all,  
  
Are there plans to implement [Karp's minimum mean cycle ](https://www.sciencedirect.com/science/article/pii/0012365X78900110) algorithm in Boost? If not, I would be willing to contribute (although my C++ is quite rusty).   
  
An example implementation is available [here](https://www.geeksforgeeks.org/karps-minimum-mean-average-weight-cycle-algorithm/). I would include recovering a minimizing cycle in the function; the added cost is small, because the main algorithm runs in O(mn) time (m edges and n vertices), while recovering the minimizer is O(n). For reference, Karp's original paper has a mistake in what concerns recovering the cycle, which was corrected by [Chatuverdi and McConnel](https://www.sciencedirect.com/science/article/pii/S0020019017301084?casa_token=NO-MxsCiH54AAAAA:cGZkyJwDDRJllboC2Wm7mDz2JX1xjJFw5Us9Ef_u9SXIEcY99Zx9DDNc2zCGK2cuaUMhR3PQxA).  
  
Thank you,  
  
Gabriel.

---

## Comment 1

> Username: ggleizer  
> Created at: 2021-07-19 08:25:00 UTC  
> Url: https://github.com/boostorg/boost/issues/537#issuecomment-882351330  

Sorry, I should have asked it in the graph library.
