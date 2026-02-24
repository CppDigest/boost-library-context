# #1242 - Numerical integration (quadrature) interface improvement [Open]

> Username: matwey  
> Created at: 2025-02-01 08:26:59 UTC  
> Updated at: 2025-02-01 18:49:55 UTC  
> Url: https://github.com/boostorg/math/issues/1242  

I would like to note that current boost.math can be further improved. The issue is the following. Currently, integrator invokes the user provided function per each point. This approach has been used in numerical integration software since ages. However many quadratures (i.e. Double exponential or Gauss) have precomputed abscissas of interest and there is a plain for-loop inside of such integrator.  
  
Currently, I am developing a software performing scientific calculation. And there is a Python interface for my software, like for many other modern scientific software. My issue is that part of the integrand function is provided by Python user, and I've found that calling Python function from C++ side is very expensive operation (way more expensive than all integrand calculating). In Python world there is a common pattern for such issue: function should accept `numpy`-like array as an argument.  
  
If I had been able to obtain all currently known abscissas, then all required values would have been calculated at a time and integrand function call number would have been reduced to a small number.

---

## Comment 1

> Username: NAThompson  
> Created at: 2025-02-01 17:46:43 UTC  
> Url: https://github.com/boostorg/math/issues/1242#issuecomment-2629044160  

The numerical integrators already work this way. For example the tanh-sinh integrator gets all the nodes/weights created in the constructor, and then many integrands can be integrated using the `.integrate` member function:  
  
```cpp  
template<class Real, class Policy = policies::policy<> >  
class tanh_sinh  
{  
public:  
    tanh_sinh(size_t max_refinements = 15, const Real& min_complement = tools::min_value<Real>() * 4)  
    : m_imp(std::make_shared<detail::tanh_sinh_detail<Real, Policy>>(max_refinements, min_complement)) {}  
  
    template<class F>  
    auto integrate(const F f, Real a, Real b, Real tolerance = tools::root_epsilon<Real>(), Real* error = nullptr, Real* L1 = nullptr, std::size_t* levels = nullptr) const ->decltype(std::declval<F>()(std::declval<Real>()));  
    template<class F>  
    auto integrate(const F f, Real a, Real b, Real tolerance = tools::root_epsilon<Real>(), Real* error = nullptr, Real* L1 = nullptr, std::size_t* levels = nullptr) const ->decltype(std::declval<F>()(std::declval<Real>(), std::declval<Real>()));  
  
    template<class F>  
    auto integrate(const F f, Real tolerance = tools::root_epsilon<Real>(), Real* error = nullptr, Real* L1 = nullptr, std::size_t* levels = nullptr) const ->decltype(std::declval<F>()(std::declval<Real>()));  
    template<class F>  
    auto integrate(const F f, Real tolerance = tools::root_epsilon<Real>(), Real* error = nullptr, Real* L1 = nullptr, std::size_t* levels = nullptr) const ->decltype(std::declval<F>()(std::declval<Real>(), std::declval<Real>()));  
};  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-02-01 18:30:00 UTC  
> Url: https://github.com/boostorg/math/issues/1242#issuecomment-2629060493  

> The numerical integrators already work this way. For example the tanh-sinh integrator gets all the nodes/weights created in the constructor, and then many integrands can be integrated using the .integrate member function.  
  
I believe that's not quite what he's asking for: he wants to get a list of abscissa values, compute (in Python) the y values at each abscissa, and then pass that vector of results back to the integrator.  
  
Unfortunately, I don't believe this can really work - the integrators are adaptive - so we don't know up front how many levels will need to be evaluated, and therefore how many abscissa values we will need.

---

## Comment 3

> Username: matwey  
> Created at: 2025-02-01 18:49:54 UTC  
> Url: https://github.com/boostorg/math/issues/1242#issuecomment-2629067679  

> Unfortunately, I don't believe this can really work - the integrators are adaptive - so we don't know up front how many levels will need to be evaluated, and therefore how many abscissa values we will need.  
  
Indeed, but you have, say, dozen of levels and thousands of abscissas.
