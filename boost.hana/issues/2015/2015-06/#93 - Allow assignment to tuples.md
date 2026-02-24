# #93 - [Tuple] Allow assignment to tuples [Closed]

> Username: ldionne  
> Created at: 2015-06-12 22:49:52 UTC  
> Updated at: 2015-08-08 12:01:27 UTC  
> Closed at: 2015-08-08 12:01:27 UTC  
> Url: https://github.com/boostorg/hana/issues/93  

Right now, Tuples can't be assigned to. However, the difficulty is to decide what should we be able to assign from. I think allowing assignment from any `Sequence` is way too open ended. However, it might make sense to assign from any other `Tuple`.
