# #102 - Surprising behavior of variant with is_constructible, is_assignable and is_convertible traits [Open]

> Username: eido79  
> Created at: 2022-11-07 13:44:29 UTC  
> Updated at: 2022-11-08 20:30:05 UTC  
> Url: https://github.com/boostorg/variant/issues/102  

is_constructible, is_assignable and is_convertible return surprising result under some conditions, suggesting that a variant might be "compatible" with a certain type, while it is, in fact, not.  
  
See https://godbolt.org/z/zzG8WaKP8 for a minimal example.

---

## Comment 1

> Username: eido79  
> Created at: 2022-11-08 20:30:05 UTC  
> Url: https://github.com/boostorg/variant/issues/102#issuecomment-1307789975  

Simplifying the problem even further (https://godbolt.org/z/Kc5nrqaP9)  
```cpp  
struct X{};  
struct test1  
{  
    test1() = default;  
    test1(X) {}  
};  
struct test2  
{  
    test2() = default;  
    explicit test2(X) {}  
};  
struct test3  
{  
    test3() = default;  
    test3(X) {}  
};  
void test_variant()  
{  
    using boost::variant;  
    static_assert(std::is_constructible_v<variant<test1>, X>); // OK  
    variant<test1> v1(X{}); // OK  
  
    static_assert(std::is_constructible_v<variant<test1, test2>, X>); // OK  
    variant<test1, test2> v2(X{}); // OK: X can be converted to test1, but not test2 as test2(X) is explicit  
  
    static_assert(std::is_constructible_v<variant<test2>, X>); // Not OK because...  
    //variant<test2> v3(X{}); // ... it doesn't compile: test2(X) is explicit  
  
    static_assert(std::is_constructible_v<variant<test1, test3>, X>); // Not OK because...  
    variant<test1, test3> v4(X{}); // ... it doesn't compile: ambiguous  
}  
```  
I think the issue is in the definition of `is_variant_constructible_from` and `is_constructible_ext`, which are used in conversion construction and assignment operator. The documentation states that  
>  T must be unambiguously **convertible** to **one** of the bounded types (i.e., T1, T2, etc.).  
  
(emphasis mine).  
  
But `is_variant_constructible_from` via `is_constructible_ext` checks if **at least one** of the bounded types is **constructible** from T, hence `std::is_constructible` returning true for ambiguous conversions or explicit constructor.
