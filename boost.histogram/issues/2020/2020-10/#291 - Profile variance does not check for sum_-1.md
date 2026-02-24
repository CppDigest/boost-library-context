# #291 - Profile variance does not check for sum_>1 [Closed]

> Username: ralfulrich  
> Created at: 2020-10-13 07:01:59 UTC  
> Updated at: 2020-11-01 22:55:25 UTC  
> Closed at: 2020-11-01 22:55:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/291  

Running a standard profile and fill it with a single entry will produce a SiGFPE when using streaming to cout:   
  
```  
auto test = boost::histogram::make_profile(boost::histogram::axis::regular<double>(  
            50, -2500, 2500));  
test_(0.,  bh::sample(100.));  
os << test;  
```  
  
produces:  
  
```  
Program received signal SIGFPE, Arithmetic exception.  
0x00005555555a0307 in boost::histogram::accumulators::mean<double>::variance (this=0x555555e9df98) at /home/work/corsika/corsika/build/ThirdParty/boost/install/boost/histogram/accumulators/mean.hpp:94  
94        value_type variance() const noexcept { return sum_of_deltas_squared_ / (sum_ - 1); }  
```  
  
which indicates that the `variance()` method is not robust at all. At least it should check whether `sum_>1` and return `0` otherwise, or something smarter (?).

---

## Comment 1

> Username: ralfulrich  
> Created at: 2020-10-13 07:08:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/291#issuecomment-707539222  

... same thing for `make_histogram_with(dense_storage<mean>(), ...)` which I tried as alternative solution.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-10-28 12:27:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/291#issuecomment-717900053  

Not at all. Returning NaN is the intended behavior. The variance is undefined when you have a single sample per bin, returning anything other than NaN makes no sense. The problem is that you have signaling NaNs enabled, which is not the default.

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-10-28 12:33:51 UTC  
> Updated at: 2020-10-28 12:34:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/291#issuecomment-717902899  

There are three options:  
1) You check for .count() > 1 before calling .variance().  
2) You turn off signaling NaNs.  
3) You write your own accumulator that implements the behavior that you want and use that with Boost.Histogram. The library was designed to make using custom Accumulators easy.

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-10-30 09:21:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/291#issuecomment-719441227  

After inspecting the code, I am reopening this. I maintain that reporting NaN for .count() == 1 is a sensible choice, but I admit that there is an annoying inconsistency, because .variance() is 0 if .count() is 0,  but .variance() is NaN if .count() is 1. This is awkward. Ideally, it should be either NaN in both cases or 0 in both cases.  
  
@ralfulrich I want to give you some context why the design is how it is, so that you understand why I cannot just "fix" this. In the C++ stdlib and in Boost we aim for the Zero Overhead Principle. Library code should be the most efficient implementation that anyone could write. It should not be possible for the user to write code which is more efficient. An example is ``std::vector::operator[]``, which does not check whether the argument index is valid, because doing these checks always would hurt also those users which know for sure that their indices are always valid.  
  
The builtin accumulators were written with this principle in mind. The consequence is that validity checks are sometimes pushed onto the user. In this case, the user is supposed to check .count() before calling .variance(). If I included that check in the call to .variance() it would incur a runtime penalty for all users, even those which know for sure that their bins always have more than 2 entries.  
  
There are three options to go about this:  
1) I change the code to always return 0 for .count() < 2. This makes the code slightly slower for everyeone  
2) I change the code to always return NaN for .count() < 2. This makes the code slightly slower for everyone.  
3) I do nothing and write in the docs that the return value of .variance() for .count() < 2 is undefined. This would be the usual C++ solution, compliant with the Zero Overhead Principle.

---

## Comment 5

> Username: HDembinski  
> Created at: 2020-11-01 22:55:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/291#issuecomment-720164785  

Behavior is now documented (option 3) in develop and master.
