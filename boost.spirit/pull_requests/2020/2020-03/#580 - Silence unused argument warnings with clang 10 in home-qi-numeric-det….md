# #580 Silence unused argument warnings with clang 10 in home/qi/numeric/det… [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2020-03-19 11:51:28 UTC  
> Updated at: 2020-03-19 15:26:07 UTC  
> Merged at: 2020-03-19 15:26:07 UTC  
> Closed at: 2020-03-19 15:26:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/580  

…ail/real_impl.hpp  
  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/include/qi.hpp:18:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/qi.hpp:24:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/qi/numeric.hpp:19:  
In file included from /data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/qi/numeric/real.hpp:23:  
/data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:176:40: error: unused parameter 'first' [-Werror,-Wunused-parameter]  
        ignore_excess_digits(Iterator& first, Iterator const& last, mpl::false_)  
                                       ^  
/data/mwrep/res/osp/Boost/20-0-0-0/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:176:63: error: unused parameter 'last' [-Werror,-Wunused-parameter]  
        ignore_excess_digits(Iterator& first, Iterator const& last, mpl::false_)  
                                                              ^  
2 errors generated.

---
