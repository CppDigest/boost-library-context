# #294 Add missing const on comparison operators [Merged]

> Username: ecatmur  
> Created at: 2022-03-29 10:57:31 UTC  
> Updated at: 2022-03-29 11:02:43 UTC  
> Merged at: 2022-03-29 11:02:20 UTC  
> Closed at: 2022-03-29 11:02:20 UTC  
> Url: https://github.com/boostorg/fiber/pull/294  

This is detected as an error in C++20 (spaceship) in clang 14, since the operators become ambiguous against reversed candidates.

---

## Comment 1

> Username: ecatmur  
> Created_at: 2022-03-29 11:02:10 UTC  
> Url: https://github.com/boostorg/fiber/pull/294#issuecomment-1081729335  

```  
src/numa/linux/topology.cpp:165:17: error: ISO C++20 considers use of overloaded operator '!=' (with operand types '(anonymous namespace)::directory_iterator' and '(anonymous namespace)::directory_iterator') to be ambiguous despite there being a unique best viable function with non-reversed arguments [-Werror,-Wambiguous-reversed-operator]  
              i != e; ++i) {  
              ~ ^  ~  
src/numa/linux/topology.cpp:75:10: note: candidate function with non-reversed arguments  
    bool operator!=( directory_iterator const& other) {  
         ^  
src/numa/linux/topology.cpp:71:10: note: ambiguous candidate function with reversed arguments  
    bool operator==( directory_iterator const& other) {  
         ^  
1 error generated.  
```

---

## Comment 2

> Username: olk  
> Created_at: 2022-03-29 11:02:25 UTC  
> Url: https://github.com/boostorg/fiber/pull/294#issuecomment-1081729580  

ty

---
