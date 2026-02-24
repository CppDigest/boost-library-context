# #53 Fix couple of -Wdeprecated-copy when using clang with std=gnu++20 or gnu++2b [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2022-12-18 14:48:22 UTC  
> Updated at: 2023-08-25 08:46:52 UTC  
> Merged at: 2023-08-25 08:46:52 UTC  
> Closed at: 2023-08-25 08:46:52 UTC  
> Url: https://github.com/boostorg/accumulators/pull/53  

======= ERROR 1 ========  
  
```  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/accumulators.hpp:12: /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulator_set.hpp:64:53: error: definition of implicit copy constructor for 'accumulator_visitor<boost::par ameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<boost::accumulators::tag::accumulator, boost::parameter::aux::tagged_argument<boost::accumulators::tag::ac cumulator, boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>>>, boost::parameter::aux::flat_like_arg_tuple<boost::accumulators::tag::sample, boost::parameter::aux::tagged_a rgument<boost::accumulators::tag::sample, const double>>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        BOOST_DELETED_FUNCTION(accumulator_visitor &operator =(accumulator_visitor const &))  
                                                    ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulator_set.hpp:303:46: note: in implicit copy constructor for 'boost::accumulators::detail::accumulator_ visitor<boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<boost::accumulators::tag::accumulator, boost::parameter::aux::tagged_argument<boost::acc umulators::tag::accumulator, boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumu lators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>>>, boost::parameter::aux::flat_like_arg_tuple<boost::accumulators::tag::sample, boost::parame ter::aux::tagged_argument<boost::accumulators::tag::sample, const double>>>>' first required here  
        fusion::for_each(this->accumulators, func);  
                                             ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulator_set.hpp:380:9: note: in instantiation of function template specialization 'boost::accumulators::a ccumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>::visit<boost::accumulators::detail::accumulator_visitor<boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<boost::accumulators::tag::accumulator, boost::parameter::aux::tagged_argument<boost::accumulators::tag::accumulator, boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>>>, boost::parameter::aux::flat_like_arg_tuple<boost::accumulators::tag::sample, boost::parameter::aux::tagged_argument<boost::accumulators::tag::sample, const double>>>>>' requested here  
      , BOOST_ACCUMULATORS_ACCUMULATOR_SET_FUN_OP  
        ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/preprocessor/repetition/repeat_from_to.hpp:35:34: note: expanded from macro 'BOOST_PP_REPEAT_FROM_TO'  
                                 ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/preprocessor/cat.hpp:22:32: note: expanded from macro 'BOOST_PP_CAT'  
                               ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/preprocessor/cat.hpp:29:34: note: expanded from macro 'BOOST_PP_CAT_I'  
                                 ^  
<scratch space>:122:1: note: expanded from here  
BOOST_PP_REPEAT_FROM_TO_1  
^  
src/Tests.cpp:349:38: note: in instantiation of function template specialization 'boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>::operator()<double>' requested here  
                _deviationAccumulator(aDeviation);  
                                     ^  
```  
  
======= ERROR 2 ========  
```  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/accumulators.hpp:12: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulator_set.hpp:35: /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulators/droppable_accumulator.hpp:52:53: error: definition of implicit copy constructor for 'add_ref_visitor<boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::accumulators::tag::accumulator, boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
            BOOST_DELETED_FUNCTION(add_ref_visitor &operator =(add_ref_visitor const &))  
                                                    ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulator_set.hpp:314:41: note: in implicit copy constructor for 'boost::accumulators::detail::add_ref_visitor<boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::accumulators::tag::accumulator, boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>>>>' first required here  
        fusion::for_each(filtered_accs, func);  
                                        ^  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/accumulators/framework/accumulator_set.hpp:175:24: note: in instantiation of function template specialization 'boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>::visit_if<boost::accumulators::detail::contains_feature_of_<boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>, boost::accumulators::detail::add_ref_visitor<boost::parameter::aux::tagged_argument_list_of_1<boost::parameter::aux::tagged_argument<boost::accumulators::tag::accumulator, boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>>>>>' requested here  
        this->template visit_if<detail::contains_feature_of_<Features> >(  
                       ^  
src/Tests.cpp:363:26: note: in instantiation of member function 'boost::accumulators::accumulator_set<double, boost::accumulators::features<boost::accumulators::tag::count, boost::accumulators::tag::min, boost::accumulators::tag::max, boost::accumulators::tag::mean, boost::accumulators::tag::moment<2>>>::accumulator_set' requested here  
    DeviationAccumulator aDeviationAccumulator;  
                         ^  
```

---
