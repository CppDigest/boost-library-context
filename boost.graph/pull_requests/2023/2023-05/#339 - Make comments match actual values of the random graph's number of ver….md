# #339 Make comments match actual values of the random graph's number of ver… [Merged]

> Username: svengato  
> Created at: 2023-05-08 02:30:12 UTC  
> Updated at: 2023-05-08 04:51:11 UTC  
> Merged at: 2023-05-08 04:45:06 UTC  
> Closed at: 2023-05-08 04:45:06 UTC  
> Url: https://github.com/boostorg/graph/pull/339  

…tices and probability of edge connection

---

## Comment 1

> Username: svengato  
> Created_at: 2023-05-08 02:32:43 UTC  
> Url: https://github.com/boostorg/graph/pull/339#issuecomment-1537654274  

I noticed the mismatches while working on pull request #315. The changes are only to comments and should not require testing.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2023-05-08 03:37:19 UTC  
> Url: https://github.com/boostorg/graph/pull/339#issuecomment-1537692185  

Even better might be to remove the numbers from the comments altogether?  
  
E.g.:  
```  
    // Generate random graph with N vertices and probability P  
    // of edge connection.  
    static const size_t N = 20;  
    static const double P = 0.1;  
```  
or  
```  
    // Generate random graph  
    static const size_t N = 20; // Number of vertices  
    static const double P = 0.1; // Probability of edge between two vertices  
```

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-05-08 04:43:51 UTC  
> Url: https://github.com/boostorg/graph/pull/339#issuecomment-1537736707  

Btw, there is a way to tell the CI not to run, which would be appropriate for these changes, by including some magic text in the commit message. I don't remember exactly what it is... instructions are probably on GitHub somewhere.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2023-05-08 04:45:17 UTC  
> Url: https://github.com/boostorg/graph/pull/339#issuecomment-1537737300  

Thanks again!

---

## Comment 5

> Username: svengato  
> Created_at: 2023-05-08 04:51:11 UTC  
> Url: https://github.com/boostorg/graph/pull/339#issuecomment-1537739995  

> Btw, there is a way to tell the CI not to run, [...]  
  
Added to my **Things to learn** list. Thanks -

---
