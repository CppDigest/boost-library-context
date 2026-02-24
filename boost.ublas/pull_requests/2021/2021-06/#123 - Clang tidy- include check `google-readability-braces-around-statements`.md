# #123 Clang tidy: include check `google-readability-braces-around-statements` [Open]

> Username: exclowd  
> Created at: 2021-06-01 11:35:35 UTC  
> Updated at: 2021-09-09 12:45:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/123  

As mentioned in issue #112. This PR adds two earlier excluded `clang-tidy` checks:   
- `google-readability-braces-around-statements`  
- `readability-braces-around-statements`  
The tests were not refactored because of incoming major changes proposed in #122. Both the tensor implementation and the examples were refactored. Moreover, since there is no definite style guide, I did not refractor too aggressively and tried to match the general style of each file to the most part so as to not make some lines stand out.

---
