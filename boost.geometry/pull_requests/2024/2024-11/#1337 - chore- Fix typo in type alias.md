# #1337 chore: Fix typo in type alias [Merged]

> Username: vissarion  
> Created at: 2024-11-05 08:46:59 UTC  
> Updated at: 2024-11-05 17:29:44 UTC  
> Merged at: 2024-11-05 13:40:19 UTC  
> Closed at: 2024-11-05 13:40:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1337  



---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2024-11-05 13:07:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1337#pullrequestreview-2415574838  

Oops, sorry. Thanks for the fix!  
How come it was not triggering a build failure...

---

## Comment 2

> Username: vissarion  
> Created_at: 2024-11-05 13:40:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1337#issuecomment-2457211947  

> Oops, sorry. Thanks for the fix! How come it was not triggering a build failure...  
  
In PRs the CI runs only minimal tests https://github.com/boostorg/geometry/blob/develop/.github/workflows/minimal.yml and the whole suite is executed after merging into develop branch.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-11-05 17:29:18 UTC  
> Updated_at: 2024-11-05 17:29:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1337#issuecomment-2457775959  

> > Oops, sorry. Thanks for the fix! How come it was not triggering a build failure...  
>   
> In PRs the CI runs only minimal tests https://github.com/boostorg/geometry/blob/develop/.github/workflows/minimal.yml and the whole suite is executed after merging into develop branch.  
  
 But I compile all also locally with CMake... however, I see it now. Exactly this one was excluded...  
```  
if (NOT APPLE)  
    # The results of these tests vary considerably on Apple/Darwin/arm64 using clang  
    foreach(item IN ITEMS  
        envelope_on_spheroid...  
```

---
