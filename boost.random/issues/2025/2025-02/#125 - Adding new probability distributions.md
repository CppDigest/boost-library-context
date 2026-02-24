# #125 - Adding new probability distributions? [Closed]

> Username: ygeunkim  
> Created at: 2025-02-01 13:52:42 UTC  
> Updated at: 2025-02-06 13:12:54 UTC  
> Closed at: 2025-02-06 13:12:53 UTC  
> Url: https://github.com/boostorg/random/issues/125  

Hi, I wonder if it is okay to add new probability distributions. I recently submitted a PR without opening an issue first. I think some discussions can help.  
I'm considering two (one in PR and one planning) that are frequently used in Bayesian literature:  
  
- Inverse Gaussian distribution in (#124) : Its density function exists in [math](https://www.boost.org/doc/libs/1_87_0/libs/math/doc/html/math_toolkit/dist_ref/dists/inverse_gaussian_dist.html) module, but random module does not provide its generator. IMHO, it would be great if there is corresponding generator. Schucany et al. (1976)'s algorithm is straightforward. It is also in Wikipedia.  
- Generalized inverse Gaussian (GIG) distribution: This is widely used in MCMC. Several sampling algorithms exist, but Hormann and Leydold (2014) is the most widely adopted. The authors have already published an R package with C code: [GIGrvg](https://github.com/unuran/GIGrvg/blob/main/GIGrvg/src/GIGrvg.c). Since its parameter region division is somewhat different from the original paper, so citing both would be appropriate.  
  
If maintainers are open to add these distributions, I'll submit additional PR for the GIG.  
Looking forward to your thoughts!

---

## Comment 1

> Username: mborland  
> Created at: 2025-02-01 19:45:48 UTC  
> Url: https://github.com/boostorg/random/issues/125#issuecomment-2629086472  

Thank you, and yes we are open to adding the distributions. I will review the PR on Monday. I took a quick look at linked C code repo. Since it is licensed under the GPL you cannot use that code or a modified version of it since the license is incompatible with the BSL-1.0 license.

---

## Comment 2

> Username: ygeunkim  
> Created at: 2025-02-02 01:17:33 UTC  
> Url: https://github.com/boostorg/random/issues/125#issuecomment-2629190051  

Thank you for the clarification.  
I'll use a different algorithm for the GIG to ensure compatibility and submit a PR accordingly.

---

## Comment 3

> Username: ygeunkim  
> Created at: 2025-02-06 13:12:53 UTC  
> Url: https://github.com/boostorg/random/issues/125#issuecomment-2639794646  

Both PRs (#124 and #126 ) are merged.
