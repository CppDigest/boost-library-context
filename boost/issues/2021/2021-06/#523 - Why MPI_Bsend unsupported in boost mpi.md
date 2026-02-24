# #523 - Why MPI_Bsend unsupported in boost mpi ? [Closed]

> Username: jarsonWu  
> Created at: 2021-06-10 07:39:33 UTC  
> Updated at: 2021-06-10 13:02:17 UTC  
> Closed at: 2021-06-10 13:02:16 UTC  
> Url: https://github.com/boostorg/boost/issues/523  

Hi, I used to use OpenMpi in my project. Recently, I find serialization in boost is great, so I want to use boost mpi in my project. Sadly, I find MPI_Bsend is unsupportted in boost mpi. This causes the code to crash when I transfer large amounts of data. So, is there a switch to enable boost mpi to support cache mode?

---

## Comment 1

> Username: mclow  
> Created at: 2021-06-10 13:02:16 UTC  
> Url: https://github.com/boostorg/boost/issues/523#issuecomment-858602267  

You opened two identical issues. Closing this one, keeping #524
