# #337 - Matrix-valued quadrature [Open]

> Username: NAThompson  
> Created at: 2020-04-04 10:59:07 UTC  
> Updated at: 2021-02-01 13:24:17 UTC  
> Url: https://github.com/boostorg/math/issues/337  

On [stackoverflow](https://stackoverflow.com/questions/61013158/integration-of-a-matrix-function-over-time-with-boost-calculate-the-controllab) some people are trying to get the quadrature to work with matrix values, which is a really cool use case, but has hit some template problems:  
  
```cpp  
#include <boost/math/quadrature/gauss.hpp>  
#include <unsupported/Eigen/MatrixFunctions>  
#include <Eigen/Geometry>  
  
typedef Eigen::Matrix<std::complex<double>, 12, 12> GramianMatrix;  
  
GramianMatrix calcControllabilityGramian(Eigen::Matrix<double, 12, 12> A, Eigen::Matrix<double, 12, 4> B) {  
  // the integral function  
  auto controllabilityGramianIntegral = [&A, &B](const double & t) -> GramianMatrix {  
      return (A * t).exp() * B * B.transpose() * (A.transpose() * t).exp();  
  };  
  // now do the integration  
  boost::math::quadrature::gauss<double, 10> integrator;  
  GramianMatrix W_t = integrator.integrate(controllabilityGramianIntegral, 0.0, 1.0);  
  
  return W_t;  
}  
```  
  
There *might* be an easy fix for this one, I recall we didn't have much trouble extending from real-valued to complex valued quadrature, so maybe this will be straightforward as well.

---

## Comment 1

> Username: ksoni-1258  
> Created at: 2020-11-29 07:43:45 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-735356591  

pleases elaborate what is to be done,so that i may work on it

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-11-29 17:07:15 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-735425164  

@ksoni-1258 : Making the code listed about work is as much as I understand as well . . .

---

## Comment 3

> Username: ksoni-1258  
> Created at: 2020-11-30 12:11:28 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-735748613  

@NAThompson   
i want to setup this repository in my computer for testing but i dont know how how to do it.  
i dont want to include boost libraries to c++ sourse header files.is there an other way?can you please help me.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-11-30 14:35:46 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-735823320  

Clone all of boost:  
  
```  
$ git clone --recursive https://github.com/boostorg/boost.git  
```

---

## Comment 5

> Username: ksoni-1258  
> Created at: 2020-12-01 12:39:43 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-736526719  

@NAThompson   
what are   boost::math::quadrature::gauss  
are these class inside other class?  
or what else does this statement mean?

---

## Comment 6

> Username: ksoni-1258  
> Created at: 2020-12-01 12:53:54 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-736533205  

@NAThompson   
what i can understand is   
"integrator" is defined as an object of class"boost::math::quadrature::gauss<double, 10>"   
is that correct?

---

## Comment 7

> Username: ksoni-1258  
> Created at: 2020-12-01 12:54:30 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-736533485  

and i request you to please assign it to me i want to work on it

---

## Comment 8

> Username: NAThompson  
> Created at: 2020-12-01 15:17:30 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-736618913  

@ksoni-1258 : Go ahead and work on it!

---

## Comment 9

> Username: ksoni-1258  
> Created at: 2020-12-01 19:02:17 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-736754541  

@NAThompson   
how does i can use the libraries before they are completely build.for example,if i change code in   
math->quadrature->gauss.hpp and want to test them with creating a main fuction somewhere.so how can i do that.or is there any better method then this.please guide me.

---

## Comment 10

> Username: NAThompson  
> Created at: 2020-12-02 17:21:37 UTC  
> Updated at: 2020-12-02 17:23:32 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-737375495  

Write a main.cpp; you don't need to compile everything because the library is header only.  
  
I put my `main.cpp` in `boost/libs/math`; e.g.,  
  
```  
$ git clone --recursive https://github.com/boostorg/boost.git  
$ cd boost/libs/math  
$ touch main.cpp  
$ g++ --std=c++17 -I./include main.cpp  
```

---

## Comment 11

> Username: ksoni-1258  
> Created at: 2020-12-02 19:48:45 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-737456306  

@NAThompson  
boost::math::quadrature::gauss<double, 10>   
what does this code above mean or what does it create?  
is it a class?if so how it is defined,i can't find any class with similar name?  
what i found was this:-  
template <class Real, unsigned N, class Policy = boost::math::policies::policy<> >  
class gauss : public detail::gauss_detail<Real, N, detail::gauss_constant_category<Real>::value>  
{  
 //integrate function defined here  
};  
does the change which is  needed to be done,is something here?  
what are the topic of c++ used in this portion/part of library,which may help me to understand this code?

---

## Comment 12

> Username: NAThompson  
> Created at: 2020-12-03 13:15:08 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-737981991  

This is a class, templated on a `Real` type (say, `float`, or `double`). The integer is the order of accuracy. The class inherits from `gauss_detail`.  
  
Rainer Kress's "Numerical Analysis" is a good place to start understanding this, then read A Tour of C++ by Bjarne Stroupstrup.

---

## Comment 13

> Username: ksoni-1258  
> Created at: 2020-12-05 18:10:02 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-739329301  

@NAThompson   
as you said above in the issue"extending from real-valued to complex valued quadrature,was an easy fix".  
could you please share me the link of the pull request you created at that time so that it can help me to understand the code better and contribute to it.

---

## Comment 14

> Username: NAThompson  
> Created at: 2020-12-05 19:33:07 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-739353921  

[Here's](https://github.com/boostorg/math/pull/154) one of them.

---

## Comment 15

> Username: ksoni-1258  
> Created at: 2020-12-25 12:31:52 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-751243113  

@NAThompson   
like in the complex issue you changed the data type with the complex number.because real numbers are also in catergory of complex number.what i mean is we can represent a real number in form of a complex number by removing the complex part.but the matrices are totally different type.so we cannot apply the same method.

---

## Comment 16

> Username: ksoni-1258  
> Created at: 2020-12-25 12:34:48 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-751243419  

what can be the other idea to take matrices in the scene.  
is there any library to iterate through each element of a matrice or any similar class(like in the eigen geometry).

---

## Comment 17

> Username: NAThompson  
> Created at: 2020-12-27 00:35:48 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-751411268  

@ksoni-1258 : I don't think I ever removed the imaginary part of the complex number to do complex-valued quadrature.

---

## Comment 18

> Username: ksoni-1258  
> Created at: 2021-01-05 05:06:48 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-754399420  

@NAThompson   
How can iterate through a 2D matrix?  
i want to replace   //Real L1 = abs(result);//    where 'result' and 'L1' are data type or class of the function input like (t*t*tan(t)) .  
 //Real L1 = abs(result);//   may work well for double,float,or any complex number,but not applicable with matrices.  
how can i do this?  
this line of code belong from. Gauss.hpp->1191.

---

## Comment 19

> Username: ksoni-1258  
> Created at: 2021-01-05 08:28:07 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-754486879  

I have solved the above issue with following code  
  
//in the main.cpp  
namespace Eigen  
{  
    auto begin(GramianMatrix& m) { return m.data(); }  
    auto end(GramianMatrix& m) { return m.data()+m.size(); }  
    auto begin(GramianMatrix const& m) { return m.data(); }  
    auto end(GramianMatrix const& m) { return m.data()+m.size(); }  
}  
  
//in gauss.hpp  
      K L1;L1=result;  
      for(auto &x : L1)x=abs(x);  
      //where "K" is as defined. // typedef decltype(f(Real(0))) K;

---

## Comment 20

> Username: NAThompson  
> Created at: 2021-01-05 13:51:21 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-754648161  

I think instead of iterating through the matrix you need to make sure the overloads are mapping appropriately.  
  
I think an `if constexpr` branch on the matrix type might help here, as you could (say) look at the type and instead of calling `abs`, you would call (say) `norm`, or whatever `Eigen` uses.

---

## Comment 21

> Username: ksoni-1258  
> Created at: 2021-01-05 16:55:18 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-754760477  

@NAThompson   
In,        boost::math::quadrature::gauss::integrate()         function from gauss.hpp  
what is pL1?for what purpose it is used?

---

## Comment 22

> Username: NAThompson  
> Created at: 2021-01-05 18:56:08 UTC  
> Updated at: 2021-01-05 18:56:37 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-754831778  

L1 generally indicates an L1 norm. The L1 norm allows one to compute the condition number of integration and hence give a correct estimate of the error that can be achieved.  
  
See Corless, [A Graduate Introduction to Numerical Methods](https://www.amazon.com/Graduate-Introduction-Numerical-Methods-Viewpoint/dp/1461484529) for more details.

---

## Comment 23

> Username: ksoni-1258  
> Created at: 2021-01-09 10:40:45 UTC  
> Updated at: 2021-01-09 10:58:54 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-757130482  

@NAThompson   
norm has several types like Euclidean norm,which will be used here i guess.  
in which the result of an n*p matrix is          squareroot(sum of squares of each element()).  
in eigen, the function     matrix.norm()  from <Eigen/Dense>  .  
in boost ,the function     boost::math::tools::l2_norm(v.begin(), v.end())       
//in which we have to separately provide .begin() and . end() iterators for any unknown data type or class.  
  
In,  //if contexpr(condition)//  
how to build the condition to find the data type or class?

---

## Comment 24

> Username: ksoni-1258  
> Created at: 2021-01-09 13:12:17 UTC  
> Updated at: 2021-01-09 13:12:32 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-757173814  

@NAThompson   
/usr/local/include/Eigen/src/Core/PlainObjectBase.h:311:7: error: static_assert failed due to requirement 'PlainObjectBase<Eigen::Matrix<double, 12, 12, 0, 12, 12> >::IsVectorAtCompileTime' "YOU_TRIED_CALLING_A_VECTOR_METHOD_ON_A_MATRIX"  
      EIGEN_STATIC_ASSERT_VECTOR_ONLY(PlainObjectBase)  
      ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/Eigen/src/Core/util/StaticAssert.h:140:3: note: expanded from macro 'EIGEN_STATIC_ASSERT_VECTOR_ONLY'  
  EIGEN_STATIC_ASSERT(TYPE::IsVectorAtCompileTime, \  
  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/Eigen/src/Core/util/StaticAssert.h:33:40: note: expanded from macro 'EIGEN_STATIC_ASSERT'  
    #define EIGEN_STATIC_ASSERT(X,MSG) static_assert(X,#MSG);  
                                       ^             ~  
/usr/local/include/Eigen/src/Core/PlainObjectBase.h:782:7: note: in instantiation of member function 'Eigen::PlainObjectBase<Eigen::Matrix<double, 12, 12, 0, 12, 12> >::resize' requested here  
      resize(size);  
      ^  
/usr/local/include/Eigen/src/Core/Matrix.h:294:22: note: in instantiation of function template specialization 'Eigen::PlainObjectBase<Eigen::Matrix<double, 12, 12, 0, 12, 12> >::_init1<double>' requested here  
      Base::template _init1<T>(x);  
                     ^  
/Users/kishansoni/Desktop/dev/project/math/include/boost/math/quadrature/gauss.hpp:1285:14: note: in instantiation of function template specialization 'Eigen::Matrix<double, 12, 12, 0, 12, 12>::Matrix<double>' requested here  
      return static_cast<K>(policies::raise_domain_error(function, "The domain of integration is not sensible; please check the bounds.", a, Policy()));  
             ^  
main.cpp:21:36: note: in instantiation of function template specialization 'boost::math::quadrature::gauss<double, 10, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy,  
      boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy,  
      boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::integrate<std::__1::function<Eigen::Matrix<double, 12, 12, 0, 12, 12>  
      (double)> >' requested here  
    GramianMatrix W_t = integrator.integrate(gramianIntegral, 0.0, 1.0);  
  
  
  
  
  
  
it came after the code   from gauss.hpp->1285  
  
return static_cast<K>(policies::raise_domain_error(function, "The domain of integration is not sensible; please check the bounds.", a, Policy()));  
  
  
  
what can be done to solv this error?

---

## Comment 25

> Username: NAThompson  
> Created at: 2021-01-09 17:13:14 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-757337077  

This shows that you have a misuse of the Eigen library.  
  
You might have bit off a bit more than you can chew with this particular task; could there be an easier one for you to do first?

---

## Comment 26

> Username: ksoni-1258  
> Created at: 2021-01-10 15:05:01 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-757491088  

@NAThompson   
since the program gets error at gauss.hpp  ->1285.  
return static_cast<K>(policies::raise_domain_error(function, "The domain of integration is not sensible; please check the bounds.", a, Policy()));  
May i build a ->   if constexpr (condition)     <-  for the matrix type.  
which wil return  f(a) and print"The domain of integration is not sensible; please check the bounds."  
  
And for this i want to learn to build condition to find the data type or class. for ->   if constexpr (condition)     <-

---

## Comment 27

> Username: NAThompson  
> Created at: 2021-01-10 15:11:47 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-757492021  

Yeah I'm fine with the `if constexpr` on the type.

---

## Comment 28

> Username: kanika-banga  
> Created at: 2021-02-01 13:24:16 UTC  
> Url: https://github.com/boostorg/math/issues/337#issuecomment-770853991  

Hi, I would like to contribute on this issue.
