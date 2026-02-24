# #113 This removes test project ID that will conflict with modular ID. [Merged]

> Username: grafikrobot  
> Created at: 2024-03-02 03:57:33 UTC  
> Updated at: 2024-03-02 14:56:21 UTC  
> Merged at: 2024-03-02 08:33:49 UTC  
> Closed at: 2024-03-02 08:33:49 UTC  
> Url: https://github.com/boostorg/parameter/pull/113  

This removes the superfluous test project ID that will conflict with the modular project IDs for all libraries. When libraries are modular they will have the standard global ID of /boost/*lib-name*. Having colliding IDs will cause build errors.  
  
#modular-boost https://github.com/users/grafikrobot/projects/1

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-03-02 14:56:09 UTC  
> Updated_at: 2024-03-02 14:56:21 UTC  
> Url: https://github.com/boostorg/parameter/pull/113#issuecomment-1974821977  

@Lastique thank you! And double thank you for merging to master also.

---
