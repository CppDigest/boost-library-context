# #2862 - Beast shouldn't have a root Jamfile [Closed]

> Username: anarthal  
> Created at: 2024-05-08 16:50:57 UTC  
> Updated at: 2024-09-01 08:30:17 UTC  
> Closed at: 2024-09-01 08:30:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2862  

### Version of Beast  
  
develop  
  
### Steps necessary to reproduce the problem  
  
The new Boost superproject has been changed to load all Jamfiles in project roots. This pours test definitions into all Boost, causing failures like https://drone.cpp.al/boostorg/mysql/660/12/3  
  
@grafikrobot should have a PR soon to fix this.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-09-01 08:30:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2862#issuecomment-2323231596  

Addressed in #2905.
