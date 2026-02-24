# #209 - BOOST_TEST_CHECK can't compare abstract classes using gcc [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-03-04 17:54:39 UTC  
> Updated at: 2019-08-02 11:14:13 UTC  
> Closed at: 2019-08-02 11:14:13 UTC  
> Url: https://github.com/boostorg/test/issues/209  

```c++  
struct abstract  
{  
    virtual ~abstract() = 0;  
    bool operator==(const abstract &) const;  
};  
  
std::ostream &operator<<(std::ostream &, const abstract &);  
  
void foo(abstract &a)  
{  
    BOOST_TEST_CHECK(a == a); // Fails  
    BOOST_CHECK_EQUAL(a, a); // Works  
}  
```  
  
[Godbolt](https://godbolt.org/z/00Y_Q3).  When compiling this code, GCC complains that it can't instantiate the member functions of `std::numeric_limits` with an abstract type.  Boost Test should check if `numeric_limits` is instantiable with a specific type before attempting to instantiate it; see https://stackoverflow.com/questions/16505782/determining-if-stdnumeric-limitst-is-safe-to-instantiate for a longer discussion of the issue and possible implementations.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-03-04 20:34:22 UTC  
> Url: https://github.com/boostorg/test/issues/209#issuecomment-469411183  

I've made a fix, annoyingly the problem does not happen on clang/OSX. Waiting for CI checks.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-03-05 20:59:01 UTC  
> Url: https://github.com/boostorg/test/issues/209#issuecomment-469854730  

Would it be possible for you to check the branch `topic/GH-209-BOOST_TEST-abstract-class-compare`. Right now it contains some failures on C++03 for comparing abstract classes (not sure I will be able to improve the support there) but otherwise seems to do the job.

---

## Comment 3

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-03-06 19:42:24 UTC  
> Url: https://github.com/boostorg/test/issues/209#issuecomment-470247729  

That branch works for me.
