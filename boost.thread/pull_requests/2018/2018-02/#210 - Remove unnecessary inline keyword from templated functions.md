# #210 Remove unnecessary inline keyword from templated functions [Merged]

> Username: austin-beer  
> Created at: 2018-02-20 16:37:22 UTC  
> Updated at: 2019-03-31 02:43:33 UTC  
> Merged at: 2018-02-21 01:12:44 UTC  
> Closed at: 2018-02-21 01:12:44 UTC  
> Url: https://github.com/boostorg/thread/pull/210  



---

## Comment 1

> Username: Lastique  
> Created_at: 2018-02-20 21:58:03 UTC  
> Url: https://github.com/boostorg/thread/pull/210#issuecomment-367134510  

What is the point of this change?

---

## Comment 2

> Username: austin-beer  
> Created_at: 2018-02-20 22:11:57 UTC  
> Url: https://github.com/boostorg/thread/pull/210#issuecomment-367138538  

It reverts some of the changes I made in 3b7ca04408f8e5b45ed5f128ae3bd4ee27d883e5. I added inline as a hint to the compiler's optimizer, but after discussing it with Vicente I realized that such hints are not necessary. Thus the inline keyword is only required by function definitions that appear in header files outside of class/struct definitions in order to ensure that the One Definition Rule is not violated.

---
