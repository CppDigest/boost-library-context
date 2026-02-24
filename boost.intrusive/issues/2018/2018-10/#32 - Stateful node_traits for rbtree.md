# #32 - Stateful node_traits for rbtree [Closed]

> Username: iassenev  
> Created at: 2018-10-25 19:49:51 UTC  
> Updated at: 2019-03-13 22:34:46 UTC  
> Closed at: 2019-03-13 21:38:49 UTC  
> Url: https://github.com/boostorg/intrusive/issues/32  

Hi!  
  
Are there any plans on making a possibility to have a stateful node_traits class?  
  
Then one can store not pointers, but indices in his custom node_traits and when there is a need for a conversion, node_traits state can be used. It is ok if there is a need to copy node_traits instance from time to time.  
  
Kind regards,  
Dmitriy

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-13 21:38:49 UTC  
> Url: https://github.com/boostorg/intrusive/issues/32#issuecomment-472616254  

Sorry for missing this issue. Response: there are no plans to support stateful node_traits because it would highly complicate the implementation.

---

## Comment 2

> Username: iassenev  
> Created at: 2019-03-13 22:34:46 UTC  
> Url: https://github.com/boostorg/intrusive/issues/32#issuecomment-472632824  

Is there any way to store in a tree indices instead of pointers? Now I just use a separate custom node_traits *class* for a range of pointers, but I need to pass somehow a beginning of a range of pointers to an instance of a node_traits class.  
  
Is there something I missed and this can be done right now without stateful node_traits?
