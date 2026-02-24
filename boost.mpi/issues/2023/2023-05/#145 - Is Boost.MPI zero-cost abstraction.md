# #145 - Is Boost.MPI zero-cost abstraction? [Closed]

> Username: wang-jinbo  
> Created at: 2023-05-29 01:41:47 UTC  
> Updated at: 2023-08-02 08:43:30 UTC  
> Closed at: 2023-08-02 08:42:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/145  

Are most parts of `Boost.MPI` zero-cost abstraction under optimization? If not, which part isn't?  
Does `Boost.MPI` provide part of abstraction in `MPI` specification higher than 1.1 standard? (I note the doc says "At present, Boost.MPI supports the majority of functionality in MPI 1.1."   
Answer appreciated!

---

## Comment 1

> Username: aminiussi  
> Created at: 2023-05-30 08:12:02 UTC  
> Url: https://github.com/boostorg/mpi/issues/145#issuecomment-1567970888  

There are two type of datatype handled by Boost.MPI:   
 - **a** those that need to be serialized and are typicaly of variable sizes.  
 - **b** those that don't.  
  
**b** includes primitive type and POD (the later need to be tagged), **a** is the rest (including variable sized datatype like container, dynamic string etc.)  
  
If you transmit fixed sized sequences of **b** objects, it will be translated in the corresponding "plain" MPI call with no overhead (note that a std::vector is not fixed sized, if you use vectors to communicate fixed sized arrays, you need to use v.data() and v.size()  to get a no cost translation, there is a common misunderstanding for that point ).  
This is a situation were no serialization  is needed and where the buffers sizes are globally known).  
  
In the over situations, the 2 following extra cost (its compared to the previous situation, but it is something user implementation will have to consider, one way or the other) are to be considered:  
 - serialization: object are to be converted to sequence of bytes in a stream  
 - if the size of the buffers are no globally known (std::vectors of any kind of data or sequence of **a** , these sizes need to be communicated first. (some optimization are possible for recuring communications, described in the doc).  
  
Not that those to situation will need to be dealt with one way or another and it is not clear better solutions can be found.  
  
Non blocking communications introduces a few extra functions call in communication probing but nothing serious.  
  
Boost MPI des not map MPI IOs nor one sided communications.  Not sure what other post 1.1 functionality (which is not the same as API, as MPI is a very low abstraction facility) could be considered.

---

## Comment 2

> Username: aminiussi  
> Created at: 2023-08-02 08:43:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/145#issuecomment-1661776104  

I'm close the issue, please reopen if there are still un answered questions.
