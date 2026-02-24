# #76 - no matching function for call to 'adjust_size_by_resizeability' in boost 1.85 [Closed]

> Username: wkotlarski  
> Created at: 2024-05-01 09:37:27 UTC  
> Updated at: 2024-05-03 08:15:08 UTC  
> Closed at: 2024-05-03 07:59:22 UTC  
> Url: https://github.com/boostorg/odeint/issues/76  

After an update to boost 1.85 we started getting errors about the `adjust_size_by_resizeability` function. This seems to be an internal boost problem, unrelated to how we actually use boost. We observe this bug both with clang 18.1.4 and gcc 13.2.1. If there's some additional info I could provide to help with this issue, please let me know.  
  
```  
In file included from /usr/include/boost/numeric/odeint/stepper/generation.hpp:28:  
In file included from /usr/include/boost/numeric/odeint/stepper/generation/generation_runge_kutta_cash_karp54.hpp:22:  
In file included from /usr/include/boost/numeric/odeint/stepper/runge_kutta_cash_karp54.hpp:24:  
/usr/include/boost/numeric/odeint/stepper/explicit_error_generic_rk.hpp:156:24: error: no matching function for call to 'adjust_size_by_resizeability'  
  156 |             resized |= adjust_size_by_resizeability( m_F[i] , x , typename is_resizeable<deriv_type>::type() );  
      |                        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/numeric/odeint/util/resizer.hpp:31:6: note: candidate function template not viable: no known conversion from 'typename is_resizeable<deriv_type>::type' (aka 'integral_constant<bool, true>') to 'boost::true_type' (aka 'integral_constant<bool, true>') for 3rd argument  
   31 | bool adjust_size_by_resizeability( ResizeWrappedState &x , const State &y , boost::true_type )  
      |      ^                                                                      ~~~~~~~~~~~~~~~~  
/usr/include/boost/numeric/odeint/util/resizer.hpp:43:6: note: candidate function template not viable: no known conversion from 'typename is_resizeable<deriv_type>::type' (aka 'integral_constant<bool, true>') to 'boost::false_type' (aka 'integral_constant<bool, false>') for 3rd argument  
   43 | bool adjust_size_by_resizeability( ResizeWrappedState & /* x */ , const State & /* y */ , boost::false_type )  
      |      ^                                                                                    ~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: Expander  
> Created at: 2024-05-03 08:15:07 UTC  
> Url: https://github.com/boostorg/odeint/issues/76#issuecomment-2092530055  

Wow, it seems to be fixed already!
