# #333 Moves the setup request execution to run_fsm [Merged]

> Username: anarthal  
> Created at: 2025-10-17 13:55:46 UTC  
> Updated at: 2025-10-20 13:56:53 UTC  
> Merged at: 2025-10-20 13:56:46 UTC  
> Closed at: 2025-10-20 13:56:46 UTC  
> Url: https://github.com/boostorg/redis/pull/333  

Adds unit tests to cover setup request execution in run_fsm  
Entails no functional change

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-17 13:59:08 UTC  
> Url: https://github.com/boostorg/redis/pull/333#issuecomment-3415734278  

This moves the setup request execution to the sans-io world. It uses an approach similar to the one I used for PINGs in the flexible health-checker.  
  
This  
  
* Simplifies the parallel group handling a lot, because we no longer have a task that finishes before the others.  
* Moves more functionality to .ipps. Concretely, after merging this and #328, all logging happens in ipp files. This will allow us to remove the `connection_logger` class completely, separating all logging functionality into the FSM ipp files.

---
