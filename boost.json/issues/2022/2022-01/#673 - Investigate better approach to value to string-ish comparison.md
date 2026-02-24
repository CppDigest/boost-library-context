# #673 - Investigate better approach to value to string-ish comparison [Open]

> Username: grisumbras  
> Created at: 2022-01-27 13:39:11 UTC  
> Updated at: 2022-01-27 17:01:59 UTC  
> Url: https://github.com/boostorg/json/issues/673  

Currently:  
```c++  
    friend  
    bool  
    operator==(  
        value const& lhs,  
        value const& rhs) noexcept  
    {  
        return lhs.equal(rhs);  
    }  
```  
  
Thus, `jv == "some string"` is equivalent to `jv == value("some string")`, which allocates.  
Consider adding an overload similar to one for `string` .

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-01-27 13:57:45 UTC  
> Updated at: 2022-01-27 13:58:42 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023237111  

I'm ok with this though. We made the decision when this was added that people who want to do this sort of thing might have to pay for allocations.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-01-27 14:40:12 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023279050  

```  
bool op==( value const&, string_view) noexcept;  
bool op==( string_view, value const&) noexcept;  
```  
but this could introduce ambiguities.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-01-27 14:55:12 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023294131  

I don't think we should do this. Instead, we should document the rationale for why we aren't doing it.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-01-27 15:34:35 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023341037  

I mis-named the issue. The problem isn't just with strings, it's also present with objects and array.  
There are 2 issues:  
1. `operator==` will silently allocate when a value is compared against a string, an `array` or an `object`.  
2. `operator==` works with `string_view`, but not with `std::string_view`.

---

## Comment 5

> Username: grisumbras  
> Created at: 2022-01-27 16:13:37 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023390043  

How about this:  
https://github.com/boostorg/json/pull/651/commits/f62332e46d6b571943009797c537ee1baa8723f2

---

## Comment 6

> Username: pdimov  
> Created at: 2022-01-27 16:17:52 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023396326  

I suspect it breaks currently working comparisons against initializer lists. Whether those are a feature is another story.

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-01-27 16:38:33 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023420020  

Is there a way to compare against initializer list without explicitly spelling out `std::initializer_list`?

---

## Comment 8

> Username: pdimov  
> Created at: 2022-01-27 17:01:59 UTC  
> Url: https://github.com/boostorg/json/issues/673#issuecomment-1023443579  

Initializer lists aren't necessarily `std::initalizer_list`s. For example, `{ 1, 2, "foo" }` is an initializer list, but not `std::initializer_list`. But it appears that I'm mistaken; Clang informs me that initializer lists aren't allowed in `operator==`.  
  
However, values can still be compared against `bool`, `int`, and `double` today: https://godbolt.org/z/GbhfbsbPb
