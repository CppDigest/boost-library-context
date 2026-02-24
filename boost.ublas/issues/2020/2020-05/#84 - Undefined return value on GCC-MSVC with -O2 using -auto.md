# #84 - Undefined return value on GCC/MSVC with -O2 using "auto" [Open]

> Username: Faaux  
> Created at: 2020-05-26 08:21:10 UTC  
> Updated at: 2021-06-11 12:30:56 UTC  
> Url: https://github.com/boostorg/ublas/issues/84  

Dear everyone,  
I hope you are doing well. I stumbled across an oddity using ublas::vector which I am not able to explain. Any information as to why this happens and how it can be prevented would be very appreciated. I have reproduced the issue in Compiler-Explorer:  
  
https://godbolt.org/z/pknvaB  
  
Description [Boost 1.73]:  
Doing simple vector arithmetic on a fixed sized vec3, I get different (wrong) results using the "auto" keyword once any optimizations (anything other than -O0) are in place on GCC and MSVC. Clang does not have this problem. I attached the code used in Compiler-Explorer to this E-Mail.  
  
Defining BOOST_UBLAS_SIMPLE_ET_DEBUG seems to also resolve the issue?  
  
Is this the right spot to ask this question or should I post this to the Mailing List aswell?  
  
  
Best,  
Faaux  
  
```cpp  
#include <boost/numeric/ublas/vector.hpp>  
#include <boost/numeric/ublas/io.hpp>  
  
 namespace ublas = boost::numeric::ublas;  
 class vec3 : public ublas::vector<double, ublas::bounded_array<double,3> >  
 {  
    typedef ublas::vector<double, ublas::bounded_array<double,3> > Base_vector;  
 public:  
    // Default construction  
    vec3 () : Base_vector(3)  
    {}  
    // Construction and assignment from a uBLAS vector expression or copy assignment  
    template <class R> vec3 (const ublas::vector_expression<R>& r) : Base_vector(r)  
    {}  
    template <class R> void operator=(const ublas::vector_expression<R>& r)  
    {  
        Base_vector::operator=(r);  
    }  
    template <class R> void operator=(const Base_vector& r)  
    {  
        Base_vector::operator=(r);  
    }  
 };  
  
int main () {  
    vec3 v;  
    v (0) = -0.5;  
    v (1) = -0.5;  
    v (2) = -0.5;  
  
    vec3 b;  
    b (0) = 0.5;  
    b (1) = 0.5;  
    b (2) = 0.5;  
  
    auto impl = (b-v)*0.5;  
    vec3 expl = (b-v)*0.5;  
    std::cout << "AutoType     : " << impl << std::endl; // Returns (1.4822e-323,1.4822e-323,1.4822e-323) (GCC 10.1 -O2)  
    std::cout << "Immediate    : " << (b-v)*0.5 << std::endl; // Returns (0.5,0.5,0.5) (GCC 10.1 -O2)  
    std::cout << "Explicit vec3: " << expl << std::endl; // Returns (0.5,0.5,0.5) (GCC 10.1 -O2)  
}

---

## Comment 1

> Username: bassoy  
> Created at: 2020-05-31 15:00:16 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-636482752  

Thanks @Faaux.  
This should not happen. We will review this bug.  
  
I have a simplified version with no extra additional type using the `tensor` type: [godbolt](https://godbolt.org/z/kN4aZH)  
  
Seems that our tests do not include optimization:  
[travis.yml](https://github.com/boostorg/ublas/blob/develop/.travis.yml)

---

## Comment 2

> Username: bassoy  
> Created at: 2020-06-02 16:17:34 UTC  
> Updated at: 2020-06-02 16:23:05 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-637659284  

Dear @Faaux   
We have discussed this matter in our gitter [channel](https://gitter.im/boostorg/ublas).  
This bug is caused by not applying the [rule of three](https://en.cppreference.com/w/cpp/language/rule_of_three) for expression template types. With optimization flags, a compiler can optimize the implicitly defined constructors such that `const&` to values are not copied.  
  
  
You can prevent such behavior by not copy-constructing expression template objects, as you already have presented in your example.  
  
We will fix this bug with the next releases by restructing and revisiting expression templates with tensors, matrices and vectors.  Either we will use [Boost.Yap](https://github.com/boostorg/yap) as already presented in our GSoC19 [project](https://github.com/BoostGSoC19/tensor/wiki/What's-new-in-New-Expression-Templates) or fully get rid of expression templates. The GSoC20 [project](https://github.com/BoostGSoC20/ublas/wiki) will provide the latest changes for Boost.uBlas.  
  
Thanks.

---

## Comment 3

> Username: Faaux  
> Created at: 2020-06-09 10:21:09 UTC  
> Updated at: 2020-06-09 10:24:33 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-641193444  

Hey @bassoy   
Good to hear :)  
  
I was wondering if there is a quick way to determine when this is happening? I am working with a pre-existing code-base and there are possibly quite a lot of situations where this could occur. I have found (by now) three situations where this is happening. This is new to us since we are currently in the process of upgrading Boost to 1.73. This could be breaking for us!  
  
Maybe a patch that deletes the copy-constructor, just to determine where we are using it?  
  
Best,  
Faaux

---

## Comment 4

> Username: Faaux  
> Created at: 2020-06-09 10:46:40 UTC  
> Updated at: 2020-06-09 13:24:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-641207039  

template<class E1, class E2, class F>  
        struct vector_binary_scalar2_traits {  
            typedef vector_binary_scalar2<E1, E2, F> expression_type;   // allow E2 to be builtin type  
    #ifndef BOOST_UBLAS_SIMPLE_ET_DEBUG  
            typedef expression_type result_type;  
    #else  
            typedef typename E1::vector_temporary_type result_type;  
    #endif  
        };  
  
As in the inital bug report defining `BOOST_UBLAS_SIMPLE_ET_DEBUG` got rid of the problem as well (even though this might just be in the presented case). Could you confirm this? What would the implications be when we compile with `BOOST_UBLAS_SIMPLE_ET_DEBUG`?  
  
Thanks again!  
  
Edit: For our use-case it might be possible to overload the according operators. Something like: https://godbolt.org/z/BHLwV9  
  
Am I missing something?

---

## Comment 5

> Username: bassoy  
> Created at: 2020-06-26 18:35:21 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-650332774  

> Hey @bassoy  
> Good to hear :)  
>   
> I was wondering if there is a quick way to determine when this is happening?  
  
As you have already found out, the problem occurs when you copy construct an expression template.  
I recommend to evaluate the expression instantaneously.   
  
> I am working with a pre-existing code-base and there are possibly quite a lot of situations where this could occur. I have found > (by now) three situations where this is happening. This is new to us since we are currently in the process of upgrading Boost to > 1.73. This could be breaking for us!  
>   
> Maybe a patch that deletes the copy-constructor, just to determine where we are using it?  
>   
  
We are working on a completely new stable solution using Boost.Yap which will be thoroughly tested and more powerful.   
  
> Best,  
> Faaux

---

## Comment 6

> Username: Faaux  
> Created at: 2021-06-01 08:01:20 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-851916265  

Hey @bassoy,  
  
any update or timeline on this?

---

## Comment 7

> Username: bassoy  
> Created at: 2021-06-04 22:30:27 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-855053221  

@Faaux thanks for asking.  
  
Yes, we have found the root cause and propose two possible solutions, see #125.   
We might fixing this issue for tensor expressions in the develop branch in the next two months.   
However, I do not want to promise any timeline for the release in the master branch as we are still in the transition phase from matrix to tensor.   
  
A quick solution is to directly use tensor, matrix or vector objects on the lhs of the expression. This will not slow down your program and you program will produce the correct results.  
  
Hope this helps.

---

## Comment 8

> Username: Faaux  
> Created at: 2021-06-11 12:30:56 UTC  
> Url: https://github.com/boostorg/ublas/issues/84#issuecomment-859548037  

Thanks for letting me know and glad you found a fix.  
  
Sadly that still is not an option to change a larger codebase and find all instances to change the auto. I have a workaround in place, thanks for your work :)
