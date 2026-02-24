# #140 avoid potential null dereferences [Closed]

> Username: dornbirndevelops  
> Created at: 2025-01-03 22:03:43 UTC  
> Updated at: 2026-02-19 20:07:53 UTC  
> Closed at: 2026-02-19 20:07:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/140  

with some warnings enabled, gcc 13 and above detect a potential null pointer dereference in the `typed_value::notify` member function.

---

## Comment 1

> Username: vprus  
> Created_at: 2025-01-03 22:54:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/140#issuecomment-2569910490  

Thanks for the PR.   
  
Can you tell why value might end up null? It seems like it's only possible due to user error and then it's better to crash than silently ignoring it?

---

## Comment 2

> Username: dornbirndevelops  
> Created_at: 2025-01-07 10:18:48 UTC  
> Url: https://github.com/boostorg/program_options/pull/140#issuecomment-2574910929  

Hi @vprus, thank you for the quick response.  
  
I created a minimum reproducible example of my problem in compiler explorer using boost 1.86.0.  
https://godbolt.org/z/efP1WxoKT  
  
I tried to stick with the original example from the docs to minimize the chance of user error.  
https://www.boost.org/doc/libs/1_86_0/doc/html/program_options/tutorial.html

---

## Comment 3

> Username: vprus  
> Created_at: 2026-01-22 11:47:38 UTC  
> Url: https://github.com/boostorg/program_options/pull/140#issuecomment-3783971565  

Thanks for the reproducer, but I'm not entirely sure it's a valid problem. The only way notify would find null value there seems to be if a user either failed to call 'store'. But in that case, having 'notify' ignore empty values would just mask the problem, making it harder to debug?

---
