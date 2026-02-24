# #1128 - Support for generalized Laguerre polynomials? [Open]

> Username: rnburn  
> Created at: 2024-05-01 22:20:10 UTC  
> Updated at: 2024-05-02 17:18:52 UTC  
> Url: https://github.com/boostorg/math/issues/1128  

Is there any support for [generalized Laguerre polynomials](https://en.wikipedia.org/wiki/Gauss%E2%80%93Laguerre_quadrature), $L_n^\alpha(\cdot)$ where $\alpha > -1$ and $\alpha$ is a floating point number?  
  
  
I'm looking for functionality similar to what [scipy.special.roots_genlaguerre ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.special.roots_genlaguerre.html) provides.  
  
For example  
```  
>>> import scipy  
>>> scipy.special.roots_genlaguerre(11, -0.5)   
                         # give me sample points and weights for a Gauss-generalized Laguerre   
                         # quadrature (this could also optionally be derived from lower level function)  
(array([ 0.05483987,  0.49517412,  1.38465574,  2.74191994,  4.5977377 ,  
        6.99939747, 10.01890828, 13.76930587, 18.44111968, 24.40196124,  
       32.59498009]), array([8.87090453e-01, 5.73942866e-01, 2.38204722e-01, 6.22807418e-02,  
       9.95679867e-03, 9.29770102e-04, 4.73102571e-05, 1.17685751e-06,  
       1.19339820e-08, 3.48867802e-11, 1.23343668e-14]))  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2024-05-02 01:03:02 UTC  
> Updated at: 2024-05-02 01:12:22 UTC  
> Url: https://github.com/boostorg/math/issues/1128#issuecomment-2089360984  

A quick `git grep` indicates that we only have standard Laguerre and associated Laguerre. I do like the idea of adding them; though I might wish the user to form the companion matrix in (say) Eigen to get the roots . . .  
  
It would appear we _do_ have support for confluent hypergeometrics; would that work for root recovery?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-05-02 11:23:13 UTC  
> Url: https://github.com/boostorg/math/issues/1128#issuecomment-2090256594  

The polynomial could be evaluated for small arguments via:  
  
```  
template <class T>  
T generalized_laguerre(unsigned n, T alpha, T x)  
{  
   return (boost::math::rising_factorial(alpha + 1, n) / boost::math::factorial<T>(n)) * boost::math::hypergeometric_1F1(-T(n), alpha + 1, x);  
}  
```  
  
But for large arguments that would quickly explode :(  
  
For the roots, I would be more inclined to figure out the coefficients of the polynomial and feed those into Eigen or similar library.

---

## Comment 3

> Username: NAThompson  
> Created at: 2024-05-02 14:43:55 UTC  
> Url: https://github.com/boostorg/math/issues/1128#issuecomment-2090685167  

@jzmaddock : I've had this code for polynomial rootfinding lying around:  
  
```cpp  
#if __has_include(<Eigen/Eigenvalues>)  
  template<typename Real=CoefficientType>  
  [[nodiscard]] std::vector<std::complex<Real>> roots() const {  
    if (c_.size() == 1) {  
      return std::vector<std::complex<Real>>();  
    }  
    // There is a temptation to split off the linear and quadratic case, since  
    // they are so easy. Resist the temptation! Your best unit tests will become  
    // tautological.  
    std::size_t n = c_.size() - 1;  
    Eigen::Matrix<Real, Eigen::Dynamic, Eigen::Dynamic> C(n, n);  
    C << Eigen::Matrix<Real, Eigen::Dynamic, Eigen::Dynamic>::Zero(n,n);  
    for (std::size_t i = 0; i < n; ++i) {  
      // Remember the class invariant c_.back() != 0?  
      // Reaping blessings right here y'all:  
      C(i, n - 1) = -c_[i] / c_.back();  
    }  
    for (std::size_t i = 0; i < n - 1; ++i) {  
      C(i + 1, i) = 1;  
    }  
    Eigen::EigenSolver<decltype(C)> es;  
    es.compute(C, /*computeEigenvectors=*/ false);  
    auto info = es.info();  
    if (info != Eigen::ComputationInfo::Success) {  
      std::ostringstream oss;  
      oss << __FILE__ << ":" << __LINE__ << ":" << __func__ << ": Eigen's eigensolver did not succeed.";  
      switch (info) {  
        case Eigen::ComputationInfo::NumericalIssue:  
          oss << " Problem: numerical issue.";  
          break;  
        case Eigen::ComputationInfo::NoConvergence:  
          oss << " Problem: no convergence.";  
          break;  
        case Eigen::ComputationInfo::InvalidInput:  
          oss << " Problem: Invalid input.";  
          break;  
        default:  
          oss << " Problem: Unknown.";  
      }  
      throw std::runtime_error(oss.str());  
    }  
    // Don't want to expose Eigen types to the rest of the world:  
    auto eigen_zeros = es.eigenvalues();  
    std::vector<std::complex<Real>> zeros(eigen_zeros.size());  
    for (std::size_t i = 0; i < zeros.size(); ++i) {  
      zeros[i] = eigen_zeros[i];  
    }  
    return zeros;  
  }  
#endif  
```  
  
Would it make sense to add it to the boost polynomial class now that we have `__has_include` support?  
  
@rnburn : Not sure you were interested in getting a bunch of random code snippets but feel free to use if it helps you . . .

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-05-02 17:18:51 UTC  
> Url: https://github.com/boostorg/math/issues/1128#issuecomment-2091106263  

> Would it make sense to add it to the boost polynomial class now that we have __has_include support?  
  
Sure, but I'll let you figure out the tests ;)
