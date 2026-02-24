# #35 Fix indefinite hang with polynomial *= itself [Merged]

> Username: kundor  
> Created at: 2016-05-02 00:55:25 UTC  
> Updated at: 2016-05-04 11:27:55 UTC  
> Merged at: 2016-05-02 17:20:02 UTC  
> Closed at: 2016-05-02 17:20:02 UTC  
> Url: https://github.com/boostorg/math/pull/35  

This code:  
  
``` C++  
#include <boost/math/tools/polynomial.hpp>  
  
int main() {  
    double acoef[] = {1., 2.};  
    boost::math::tools::polynomial<double> a(acoef, 1);  
    a *= a;  
    return 0;  
}  
```  
  
will hang indefinitely (either with Boost 1.59.0, or with the current develop branch.)  
This is because `polynomial::operator *=(const polynomial<U>& value)` goes through the elements of `value` in a loop, pushing back some new elements on `m_data`; when `value` is `*this`, this grows the list we are looping through, so it goes on forever.  
  
A one-character fix is to delete the `&` in the function signature.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-05-02 01:15:50 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216089058  

Thanks for catching this, Nick. I'm not sure we want to always copy the right-hand operand, and there are a couple of copies going on in there already with `base` and `t`, so I'd hope that we could safely handle self-multiplication by rearranging those copies without introducing a new one.

---

## Comment 2

> Username: kundor  
> Created_at: 2016-05-02 03:29:34 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216099337  

A possibility without the copy is in this second commit 9c8e3f2. Essentially I just reversed the order of the loops, so that they go from the highest-order terms down. That way, if `value` is `*this`, we don't overwrite `value[i]` until after we're done using it.  
  
I also eliminated the copying into `t`.  
  
It is necessary for the loop index variables to be `int` instead of `size_type` because unsigned types always satisfy the `i >= 0` condition.  
It's necessary to subtract `base` at the end to remove the initial values in `m_data` which were added to. Instead, `m_data` could be set to all 0s to start, but that would clobber `value` in the event that `value` is `*this`.

---

## Comment 3

> Username: kundor  
> Created_at: 2016-05-02 03:38:44 UTC  
> Updated_at: 2016-05-02 03:40:42 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216099987  

Another possibility would be   
  
``` C++  
std::vector<T> prod(size() + value.size() - 1);  
for (size_type i = 0; i < value.size(); ++i)  
    for (size_type j = 0; j < size(); ++j)  
        prod[i+j] += m_data[j] * value[i];  
m_data.swap(prod);  
return *this;  
```  
  
This eliminates the copy `base` and the need for the subtraction.

---

## Comment 4

> Username: kundor  
> Created_at: 2016-05-02 04:00:05 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216101522  

The more I thought about this third possibility, the more I liked it, so I committed it (627a4ce). With either the original code doing `push_back`, or the 9c8e3f2 version doing `resize`, there was bound to be a reallocation. This version has that same reallocation, but no others, and it avoids copying the previous members of `m_data` only to have to subtract them.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2016-05-02 04:06:34 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216101934  

This looks great. Have you run the polynomial unit tests with it?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2016-05-02 12:01:33 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216218703  

Just to say it fails the tests - I haven't investigated why yet.

---

## Comment 7

> Username: kundor  
> Created_at: 2016-05-02 13:54:02 UTC  
> Updated_at: 2016-05-02 14:11:06 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216241933  

It occurred to me that the original code did not assume that `T` could be default-constructed with a zero value, so I removed that assumption here, too.  
  
Can you tell me how I can run the polynomial unit tests? I'm not familiar with the build system.  
  
Addendum: I managed to compile and run `test_polynomial.cpp` and it says "Running 38 test cases... **\* No errors detected"

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2016-05-02 17:15:25 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216298200  

Testing now.... looking good so far.  
  
For the record the way to run the tests, assuming you have already bootstrapped bjam in the boost root directory, then:  
  
cd libs/math/test  
../../../b2 toolsetname -jN  
  
where "toolsetname" is the name of the compiler to test - gcc msvc etc, and N is the number of tasks to run in parallel (and trust me you will want to run in parallel).

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2016-05-04 01:49:50 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216718808  

John, is it worth trying to get this bugfix into the 1.61 release?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2016-05-04 11:27:55 UTC  
> Url: https://github.com/boostorg/math/pull/35#issuecomment-216833375  

On 04/05/2016 02:49, Jeremy W. Murphy wrote:  
  
> John, is it worth trying to get this bugfix into the 1.61 release?  
  
Much too late, the RC's are already out, if we wait for every bug report   
to be fixed we'll never release (we've been there and done that!)  
  
John.

---
