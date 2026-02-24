# #79 - MSVC + std::complex with boost::multiprecision while using Eigen(GeneralizedEigenSolver). [Closed]

> Username: 2DaT  
> Created at: 2018-08-25 17:32:28 UTC  
> Updated at: 2018-08-28 13:54:20 UTC  
> Closed at: 2018-08-28 13:54:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/79  

MSVC constexpr complie errors coming from std::abs for std::complex, which is used in generalized eigen solver. Even if those errors are mitigated, some constants in implementation are not suitable for multiprecision. Temprorary workaround in code included.  
```  
Error	C2127	'_Root2': illegal initialization of 'constexpr' entity with a non-constant expression	c:\program files (x86)\microsoft visual studio\2017\professional\vc\tools\msvc\14.13.26128\include\xcomplex	  
```  
```  
#include <boost\multiprecision\cpp_bin_float.hpp>  
#include <boost\multiprecision\eigen.hpp>  
#include <Eigen\Dense>  
#include <iostream>  
  
  
typedef boost::multiprecision::cpp_bin_float_100 bigfloat;  
  
//Possible workaround  
//template<>  
//bigfloat std::abs(const std::complex<bigfloat>& a)  
//{  
//	return boost::multiprecision::hypot(a.real(),a.imag());  
//}  
  
int main()  
{  
	using namespace Eigen;  
	typedef Matrix<bigfloat, Dynamic, Dynamic>  BfMatrix;  
	BfMatrix my_matrix{ 2,2 };  
	my_matrix << 3, -2, 4, 1;  
	GeneralizedEigenSolver<BfMatrix> eigenSolver{ my_matrix, BfMatrix::Identity(2, 2), true };  
	std::cout << "Eigenvalues :\n" << eigenSolver.eigenvalues() << '\n';  
	return 0;  
}  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-25 18:25:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/79#issuecomment-415987785  

Ugh, you can't do that - which is to say you cannot instantiate std::complex on any type other than float/double/long double - that's a limitation imposed by the standard.  Even if the code compiles it won't necessarily do the right thing - for example the msvc implementation of `<complex>` is littered with double precision constants for Pi etc.  Some are constexpr and will fail to compile, but not all - and those will just silently do the wrong thing when used with a higher precision type.  The error is in GeneralizedEigenSolver.h which hardcodes:  
  
```  
    typedef std::complex<RealScalar> ComplexScalar;  
```  
I'll try and file a bug report against Eigen for this.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-25 18:28:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/79#issuecomment-415987979  

Ugh, apparently filing an Eigen bug report is not so easy - do you already have an account?

---

## Comment 3

> Username: 2DaT  
> Created at: 2018-08-26 03:44:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/79#issuecomment-416011696  

Not yet, but i will try to fill a bug report.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-26 11:13:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/79#issuecomment-416031165  

OK I've asked for access as well.

---

## Comment 5

> Username: 2DaT  
> Created at: 2018-08-26 13:52:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/79#issuecomment-416040509  

[Sent it](http://eigen.tuxfamily.org/bz/show_bug.cgi?id=1589)
