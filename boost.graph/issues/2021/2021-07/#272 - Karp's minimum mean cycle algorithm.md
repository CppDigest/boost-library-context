# #272 - Karp's minimum mean cycle algorithm [Open]

> Username: ggleizer  
> Created at: 2021-07-19 08:26:26 UTC  
> Updated at: 2022-01-20 23:59:37 UTC  
> Url: https://github.com/boostorg/graph/issues/272  

Dear all,  
  
Are there plans to implement [Karp's minimum mean cycle ](https://www.sciencedirect.com/science/article/pii/0012365X78900110) algorithm in Boost? If not, I would be willing to contribute (although my C++ is quite rusty).   
  
An example implementation is available [here](https://www.geeksforgeeks.org/karps-minimum-mean-average-weight-cycle-algorithm/). I would include recovering a minimizing cycle in the function; the added cost is small, because the main algorithm runs in O(mn) time (m edges and n vertices), while recovering the minimizer is O(n). For reference, Karp's original paper has a mistake in what concerns recovering the cycle, which was corrected by [Chatuverdi and McConnel](https://www.sciencedirect.com/science/article/pii/S0020019017301084?casa_token=NO-MxsCiH54AAAAA:cGZkyJwDDRJllboC2Wm7mDz2JX1xjJFw5Us9Ef_u9SXIEcY99Zx9DDNc2zCGK2cuaUMhR3PQxA).  
  
Thank you,  
  
Gabriel.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2021-07-19 12:02:41 UTC  
> Url: https://github.com/boostorg/graph/issues/272#issuecomment-882491374  

If there is no existing issue or pull request open for it then presumably no-one else is working on it.  
Sounds like a good contribution to Boost.Graph. You don't have to be a C++ wizard to contribute, just be prepared for a lot of _feedback_. :)  
The only problem is that the library is in a bit of a dormant state -- it's failing the CI and I don't know if any contributions will get merged until it's passing again.

---

## Comment 2

> Username: ggleizer  
> Created at: 2021-07-19 19:32:32 UTC  
> Url: https://github.com/boostorg/graph/issues/272#issuecomment-882804027  

Thanks, Jeremy. I will (slowly) give it a try then.

---

## Comment 3

> Username: GYuvanShankar  
> Created at: 2021-12-17 13:36:52 UTC  
> Url: https://github.com/boostorg/graph/issues/272#issuecomment-996728241  

Greetings,  
  
I have tried my implementation of this algorithm  at https://github.com/GYuvanShankar/boost_graph_algorithm/blob/master/main.cpp.   
Please do check it out, and let me know if I can continue to open a pull request on this matter.  
  
Thank you.  
G Yuvan Shankar
