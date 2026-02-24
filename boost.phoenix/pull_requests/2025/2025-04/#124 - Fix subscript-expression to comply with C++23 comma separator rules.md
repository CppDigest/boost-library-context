# #124 Fix subscript-expression to comply with C++23 comma separator rules [Open]

> Username: NEIL-smtg  
> Created at: 2025-04-16 06:16:46 UTC  
> Updated at: 2025-04-16 06:16:46 UTC  
> Url: https://github.com/boostorg/phoenix/pull/124  

Creating this PR in response to https://github.com/boostorg/phoenix/issues/123  
  
In C++23, the grammar for subscript-expressions was updated to accept an expression-list, which causes the top-level comma (,) to be treated as a separator rather than part of a single expression. This leads to parsing issues when using comma expressions directly inside subscript operators.  
  
To address this, affected expressions have been wrapped in parentheses to ensure the intended evaluation order is preserved and to maintain compatibility with C++23 and beyond.  
  
Example fix:  
```c++  
// Before (invalid in C++23):  
while_(arg1--)  
[  
    cout << arg1 << ", ",  
    ++ref(x)  
]  
  
// After (valid in C++23):  
while_(arg1--)  
[  
    (cout << arg1 << ", ", ++ref(x))  
]  
```

---
