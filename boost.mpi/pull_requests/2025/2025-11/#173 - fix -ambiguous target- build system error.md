# #173 fix "ambiguous target" build system error [Merged]

> Username: grisumbras  
> Created at: 2025-11-25 11:00:28 UTC  
> Updated at: 2025-12-02 13:52:11 UTC  
> Merged at: 2025-12-02 13:52:11 UTC  
> Closed at: 2025-12-02 13:52:11 UTC  
> Url: https://github.com/boostorg/mpi/pull/173  

The `message` rule is declaring a target on its own. `alias boost_mpi` was declaring it a second time, hence the ambiguity.  
  
I also added depdency on `boost_mpi` to `boost_mpi_python` and `mpi` in order for the warning to be printed if only those secondary libraries are requested.

---
