# #130 - Why MPI_Bsend unsupported in boost mpi ? [Closed]

> Username: jarsonWu  
> Created at: 2021-06-10 07:40:22 UTC  
> Updated at: 2023-08-02 08:45:12 UTC  
> Closed at: 2023-08-02 08:45:12 UTC  
> Url: https://github.com/boostorg/mpi/issues/130  

Hi, I used to use OpenMpi in my project. Recently, I find serialization in boost is great, so I want to use boost mpi in my project. Sadly, I find MPI_Bsend is unsupportted in boost mpi. This causes the code to crash when I transfer large amounts of data. So, is there a switch to enable boost mpi to support cache mode?

---

## Comment 1

> Username: jeffhammond  
> Created at: 2022-11-18 09:01:17 UTC  
> Url: https://github.com/boostorg/mpi/issues/130#issuecomment-1319721277  

I am not a Boost.MPI person, but an MPI Forum person...  
  
`MPI_Bsend` is problematic due to lack of scoping of the buffer.  This makes it unsafe to use in libraries, since one library cannot know if another library is using the buffer.  
  
In any case, the lack of Bsend is not why your application crashes when you transfer large amount of data.  You have another problem there, which is almost certainly in your application, not MPI (or Boost.MPI).
