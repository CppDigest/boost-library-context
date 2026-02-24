# #42 Improve natvis [Merged]

> Username: K-ballo  
> Created at: 2024-03-23 19:07:20 UTC  
> Updated at: 2024-03-25 13:13:45 UTC  
> Merged at: 2024-03-25 00:22:21 UTC  
> Closed at: 2024-03-25 00:22:21 UTC  
> Url: https://github.com/boostorg/variant2/pull/42  

- extend support from 10 to 40 alternatives,  
- split single-buffer vs double-buffer definitions,  
- adjust display string to follow MS definition for std::variant

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-03-23 19:16:51 UTC  
> Url: https://github.com/boostorg/variant2/pull/42#issuecomment-2016580105  

@vinniefalco Any objections to this?

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-03-24 13:28:56 UTC  
> Url: https://github.com/boostorg/variant2/pull/42#issuecomment-2016810521  

Looks better than my version, so if this works I would give it a three thumbs up.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2024-03-24 13:29:37 UTC  
> Url: https://github.com/boostorg/variant2/pull/42#issuecomment-2016810675  

@K-ballo did you test this?

---

## Comment 4

> Username: K-ballo  
> Created_at: 2024-03-24 14:55:17 UTC  
> Url: https://github.com/boostorg/variant2/pull/42#issuecomment-2016835228  

> @K-ballo did you test this?  
  
Here are some examples:  
```  
variant<bool, int, double> v0{ 0.0 }; // single storage  
variant<bool, int, double, int, int, int, int, int, int, int> v1{ 1.0 }; // 10 elements storage  
variant<evil, bool, int, double> v2{ 2.0 }; // double storage  
variant<evil, bool, int, double> v3; // double storage  
v3.emplace<double>(3.0); // use second storage  
```  
![image](https://github.com/boostorg/variant2/assets/1700391/54ff7724-f906-4045-a2ec-e27c8d445d5a)

---
