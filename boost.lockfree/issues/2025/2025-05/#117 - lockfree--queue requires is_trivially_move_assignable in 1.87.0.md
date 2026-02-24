# #117 - lockfree::queue requires is_trivially_move_assignable in 1.87.0 [Closed]

> Username: praetorian20  
> Created at: 2025-05-09 21:28:36 UTC  
> Updated at: 2025-05-19 07:44:47 UTC  
> Closed at: 2025-05-19 07:44:47 UTC  
> Url: https://github.com/boostorg/lockfree/issues/117  

Hello,  
I left a comment [here](https://github.com/boostorg/lockfree/commit/6a70a658aa45c2ec1794c9ef3d9e7aa19a094400#diff-a004ff9cc7ad31a545262f43f9c1c66a275ee62838ca5c90d83c86457e57e71bR87) about this issue.  
  
Basically, I have a type that satisfies `std::is_trivially_copy_assignable`, but does not satisfy `std::is_trivially_move_assignable`. I was able to use this in `lockfree::queue` until 1.85.0, but the code fails to compile with 1.87.0.  
  
Is it possible to replace `std::is_trivially_assignable` with `std::is_trivially_copy_assignable`, and fallback to copy-assign instead of move-assign if needed within the lockfree code?

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-05-10 07:29:11 UTC  
> Url: https://github.com/boostorg/lockfree/issues/117#issuecomment-2868576143  

interesting. i think the type doesn't need to be trivially move-assignable, but trivial copy is required by the michael/scott algorithm. let me check
