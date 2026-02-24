# #262 - Ability to choose static_vector's size_type [Closed]

> Username: psiha  
> Created at: 2023-12-21 12:40:36 UTC  
> Updated at: 2024-10-12 22:17:30 UTC  
> Closed at: 2024-10-12 22:17:30 UTC  
> Url: https://github.com/boostorg/container/issues/262  

and/or simply deduce it automatically to the smallest required integer (based on the static size and/or size/alignment of T).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-10-12 22:17:30 UTC  
> Url: https://github.com/boostorg/container/issues/262#issuecomment-2408715221  

Thanks for the suggestion. Implemented in commit https://github.com/boostorg/container/commit/13603d7de15d8b410743173664cd579d38e221a9, will be released in Boost 1.87.
