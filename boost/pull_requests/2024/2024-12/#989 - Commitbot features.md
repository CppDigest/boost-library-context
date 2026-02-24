# #989 Commitbot features [Merged]

> Username: sdarwin  
> Created at: 2024-12-11 16:20:36 UTC  
> Updated at: 2025-10-11 19:08:52 UTC  
> Merged at: 2025-10-11 19:08:52 UTC  
> Closed at: 2025-10-11 19:08:52 UTC  
> Url: https://github.com/boostorg/boost/pull/989  

Modifies the commit-bot so the variables `block_develop` and `block_master` require the value of "yes" .    
  
@mclow  merge this when you like.     
  
How about delaying until after boost 1.87.0 has been released, and after the bot has been re-enabled.     
  
For this cycle, completely remove the variable `block_master`.  Next time it can merely be set to "no".

---
