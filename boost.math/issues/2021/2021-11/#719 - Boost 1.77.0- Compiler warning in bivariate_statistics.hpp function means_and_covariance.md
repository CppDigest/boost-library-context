# #719 - Boost 1.77.0: Compiler warning in bivariate_statistics.hpp function means_and_covariance [Closed]

> Username: Frank-Schmitt  
> Created at: 2021-11-10 22:22:12 UTC  
> Updated at: 2021-11-25 17:06:39 UTC  
> Closed at: 2021-11-25 17:06:39 UTC  
> Url: https://github.com/boostorg/math/issues/719  

When calling means_and_covariance with Real = double, Visual Studio complains about potential data loss in a conversion from size_t to double. Reason is this:  
  
```  
            using ReturnType = std::tuple<Real, Real, Real, Real>;  
            ReturnType temp = detail::means_and_covariance_seq_impl<ReturnType>(std::begin(u), std::end(u), std::begin(v), std::end(v));  
```  
  
detail::means_and_covariance_seq_impl however returns in the last element of the tuple always a size_t   
  
```  
// See Equation III.9 of "Numerically Stable, Single-Pass, Parallel Statistics Algorithms", Bennet et al.  
template<typename ReturnType, typename ForwardIterator>  
ReturnType means_and_covariance_seq_impl(ForwardIterator u_begin, ForwardIterator u_end, ForwardIterator v_begin, ForwardIterator v_end)  
{  
    using Real = typename std::tuple_element<0, ReturnType>::type;  
  
    Real cov = 0;  
    ForwardIterator u_it = u_begin;  
    ForwardIterator v_it = v_begin;  
    Real mu_u = *u_it++;  
    Real mu_v = *v_it++;  
    std::size_t i = 1;  
  
    while(u_it != u_end && v_it != v_end)  
    {  
        Real u_temp = (*u_it++ - mu_u)/(i+1);  
        Real v_temp = *v_it++ - mu_v;  
        cov += i*u_temp*v_temp;  
        mu_u = mu_u + u_temp;  
        mu_v = mu_v + v_temp/(i+1);  
        i = i + 1;  
    }  
  
    if(u_it != u_end || v_it != v_end)  
    {  
        throw std::domain_error("The size of each sample set must be the same to compute covariance");  
    }  
  
    return std::make_tuple(mu_u, mu_v, cov/i, i);  
}  
```  
  
so the code should be changed to   
  
```  
            using ReturnType = std::tuple<Real, Real, Real, size_t>;  
            ReturnType temp = detail::means_and_covariance_seq_impl<ReturnType>(std::begin(u), std::end(u), std::begin(v), std::end(v));  
```  
  
similar for the other if case in the function

---

## Comment 1

> Username: mborland  
> Created at: 2021-11-25 10:19:30 UTC  
> Url: https://github.com/boostorg/math/issues/719#issuecomment-979064108  

This should have been fixed in https://github.com/boostorg/math/pull/669 and will come out in the 1.78 release shortly.
