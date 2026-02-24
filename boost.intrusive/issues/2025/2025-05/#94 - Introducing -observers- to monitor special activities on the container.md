# #94 - Introducing "observers" to monitor special activities on the container. [Open]

> Username: SmallHuaZi  
> Created at: 2025-05-30 19:21:57 UTC  
> Updated at: 2025-05-30 19:23:31 UTC  
> Url: https://github.com/boostorg/intrusive/issues/94  

Hello, maintainers. Recently i’ve been studying some fascinating kernel-level design patterns in Linux and Zircon, particularly augmented RB-trees and SubtreeObserver. These mechanisms enable efficient monitoring and reactive updates for specific operations on a container.For example, in Linux, the augmented RB-tree allows us reactively update additional scheduling metadata when balance actions happened on the tree.  
So is it feasible to implement it as a new option in the existing container design?
