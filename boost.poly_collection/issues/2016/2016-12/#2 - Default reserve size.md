# #2 - Default reserve size [Open]

> Username: holocronweaver  
> Created at: 2016-12-23 04:11:50 UTC  
> Updated at: 2016-12-23 04:11:50 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/2  

I would like to suggest adding a user-configurable default reserve size which applies to all future allocations, including types which have not been registered yet.   
  
Motivation: As is, having to explicitly `reserve<EachSubType>(theSameValue)` somewhat defeats the polymorphic goal of the library. In most of my use cases, I simply want to set a per-segment initial size and leave it at that.   
  
I like having the option of performing per-segment reserves if I choose, so I think leaving `reserve` as-is is fine, and adding a `setDefaultReserve` would solve the issue.
