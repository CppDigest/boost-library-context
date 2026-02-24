# #88 - Since 1.85 - flag_list can't be defined with boost::mp11::mp_list [Open]

> Username: PiotrNycz  
> Created at: 2025-03-20 18:19:30 UTC  
> Updated at: 2025-03-20 18:19:30 UTC  
> Url: https://github.com/boostorg/msm/issues/88  

`boost::mp11::mp_list` is mpl-compatible type-list and we have habit to use it for big state machines (20+ transitions).  
It is really not necessary, but folks in my project use this mp_list also for other things - like initial_state or flag_list.  
  
When we switched to 1.86 - flag_list defined with mp_list stops compiling with this errors  
  
> In file included from /app/boost/include/boost/mp11/utility.hpp:14,  
>                  from /app/boost/include/boost/parameter/aux_/result_of0.hpp:14,  
>                  from /app/boost/include/boost/parameter/aux_/arg_list.hpp:51,  
>                  from /app/boost/include/boost/parameter/parameters.hpp:22,  
>                  from /app/boost/include/boost/parameter.hpp:11,  
>                  from /app/boost/include/boost/msm/back/state_machine.hpp:54,  
>                  from <source>:1:  
> /app/boost/include/boost/mp11/detail/mp_front.hpp: In substitution of 'template<class L> using boost::mp11::mp_front = typename boost::mp11::detail::mp_front_impl::type [with L = boost::mp11::mp_list<>]':  
> /app/boost/include/boost/mp11/list.hpp:149:25:   required by substitution of 'template<class L> using boost::mp11::mp_first = boost::mp11::mp_front<L> [with L = boost::mp11::mp_list<>]'  
>   149 | template<class L> using mp_first = mp_front<L>;  
>       |                         ^~~~~~~~  
> /app/boost/include/boost/mp11/detail/mpl_common.hpp:30:11:   required from 'struct boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> >'  
>    30 |     using type = mp11::mp_first<L>;  
>       |           ^~~~  
> /app/boost/include/boost/fusion/adapted/mpl/mpl_iterator.hpp:23:12:   required from 'struct boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > >'  
>    23 |     struct mpl_iterator  
>       |            ^~~~~~~~~~~~  
> /app/boost/include/boost/type_traits/is_base_and_derived.hpp:226:5:   required from 'const bool boost::detail::is_base_and_derived_impl<boost::fusion::iterator_root, boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > > >::value'  
>   226 |     BOOST_STATIC_CONSTANT(bool, value = (BOOST_IS_BASE_OF(B,D) && ! ::boost::is_same<ncvB,ncvD>::value));  
>       |                                          ^~~~~~~~~~~~~~~~  
> /app/boost/include/boost/type_traits/is_base_of.hpp:24:11:   required from 'const bool boost::detail::is_base_of_imp<boost::fusion::iterator_root, boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > > >::value'  
>    24 |           BOOST_STATIC_CONSTANT(bool, value = (  
>       |           ^~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/type_traits/is_base_of.hpp:30:48:   [ skipping 7 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
> /app/boost/include/boost/mpl/for_each.hpp:78:26:   required from 'static void boost::mpl::aux::for_each_impl<false>::execute(Iterator*, LastIterator*, TransformFunc*, F) [with Iterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::s_item<State1, boost::mpl::set0<> > >; LastIterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::set0<> >; TransformFunc = boost::msm::wrap<mpl_::arg<1> >; F = boost::msm::back::state_machine<Front>::init_flags<Flag1>]'  
>    78 |         aux::unwrap(f, 0)(boost::get(x));  
>       |         ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~  
> /app/boost/include/boost/mpl/for_each.hpp:82:22:   required from 'static void boost::mpl::aux::for_each_impl<false>::execute(Iterator*, LastIterator*, TransformFunc*, F) [with Iterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > > >; LastIterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::set0<> >; TransformFunc = boost::msm::wrap<mpl_::arg<1> >; F = boost::msm::back::state_machine<Front>::init_flags<Flag1>]'  
>    81 |         for_each_impl<boost::is_same<iter,LastIterator>::value>  
>       |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>    82 |             ::execute( static_cast<iter*>(0), static_cast<LastIterator*>(0), static_cast<TransformFunc*>(0), f);  
>       |             ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/mpl/for_each.hpp:105:18:   required from 'void boost::mpl::for_each(F, Sequence*, TransformOp*) [with Sequence = s_item<State<2>, s_item<State1, set0<> > >; TransformOp = boost::msm::wrap<mpl_::arg<1> >; F = boost::msm::back::state_machine<Front>::init_flags<Flag1>]'  
>   104 |     aux::for_each_impl< boost::is_same<first,last>::value >  
>       |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>   105 |         ::execute(static_cast<first*>(0), static_cast<last*>(0), static_cast<TransformOp*>(0), f);  
>       |         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/msm/back/state_machine.hpp:2386:29:   required from 'bool (** boost::msm::back::state_machine<A0, A1, A2, A3, A4>::get_entries_for_flag() const)(const library_sm&) [with Flag = Flag1; A0 = Front; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; flag_handler = bool (*)(const boost::msm::back::state_machine<Front>&)]'  
>  2385 |             (::boost::mpl::for_each<state_list, boost::msm::wrap< ::boost::mpl::placeholders::_1> >  
>       |              ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>  2386 |                             (init_flags<Flag>(flags_entries)),  
>       |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/msm/back/state_machine.hpp:1518:95:   required from 'bool boost::msm::back::state_machine<A0, A1, A2, A3, A4>::is_flag_active() const [with Flag = Flag1; A0 = Front; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_]'  
>  1518 |         return FlagHelper<Flag,(nr_regions::value>1)>::helper(*this,get_entries_for_flag<Flag>());  
>       |                                                                     ~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
>   
> > <source>:42:43:   required from here  
>   
>    42 |     std::cout << fsm.is_flag_active<Flag1>();  
>       |                  ~~~~~~~~~~~~~~~~~~~~~~~~~^~  
> /app/boost/include/boost/mp11/detail/mp_front.hpp:45:25: error: no type named 'type' in 'struct boost::mp11::detail::mp_front_impl<boost::mp11::mp_list<> >'  
>    45 | template<class L> using mp_front = typename detail::mp_front_impl<L>::type;  
>       |                         ^~~~~~~~  
> In file included from /app/boost/include/boost/mp11/mpl_list.hpp:11,  
>                  from <source>:6:  
> /app/boost/include/boost/mp11/detail/mpl_common.hpp: In instantiation of 'struct boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> >':  
> /app/boost/include/boost/fusion/adapted/mpl/mpl_iterator.hpp:23:12:   required from 'struct boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > >'  
>    23 |     struct mpl_iterator  
>       |            ^~~~~~~~~~~~  
> /app/boost/include/boost/type_traits/is_base_and_derived.hpp:226:5:   required from 'const bool boost::detail::is_base_and_derived_impl<boost::fusion::iterator_root, boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > > >::value'  
>   226 |     BOOST_STATIC_CONSTANT(bool, value = (BOOST_IS_BASE_OF(B,D) && ! ::boost::is_same<ncvB,ncvD>::value));  
>       |                                          ^~~~~~~~~~~~~~~~  
> /app/boost/include/boost/type_traits/is_base_of.hpp:24:11:   required from 'const bool boost::detail::is_base_of_imp<boost::fusion::iterator_root, boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > > >::value'  
>    24 |           BOOST_STATIC_CONSTANT(bool, value = (  
>       |           ^~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/type_traits/is_base_of.hpp:30:48:   required from 'struct boost::is_base_of<boost::fusion::iterator_root, boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > > >'  
>    30 |    template <class Base, class Derived> struct is_base_of  
>       |                                                ^~~~~~~~~~  
> /app/boost/include/boost/fusion/support/is_iterator.hpp:18:12:   required from 'struct boost::fusion::is_fusion_iterator<boost::fusion::mpl_iterator<boost::mpl::aux::mp11_iterator<boost::mp11::mp_list<> > > >'  
>    18 |     struct is_fusion_iterator : is_base_of<iterator_root, T> {};  
>       |            ^~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/mpl/if.hpp:63:11:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
> /app/boost/include/boost/mpl/for_each.hpp:78:26:   required from 'static void boost::mpl::aux::for_each_impl<false>::execute(Iterator*, LastIterator*, TransformFunc*, F) [with Iterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::s_item<State1, boost::mpl::set0<> > >; LastIterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::set0<> >; TransformFunc = boost::msm::wrap<mpl_::arg<1> >; F = boost::msm::back::state_machine<Front>::init_flags<Flag1>]'  
>    78 |         aux::unwrap(f, 0)(boost::get(x));  
>       |         ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~  
> /app/boost/include/boost/mpl/for_each.hpp:82:22:   required from 'static void boost::mpl::aux::for_each_impl<false>::execute(Iterator*, LastIterator*, TransformFunc*, F) [with Iterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > > >; LastIterator = boost::mpl::s_iter<boost::mpl::s_item<State<2>, boost::mpl::s_item<State1, boost::mpl::set0<> > >, boost::mpl::set0<> >; TransformFunc = boost::msm::wrap<mpl_::arg<1> >; F = boost::msm::back::state_machine<Front>::init_flags<Flag1>]'  
>    81 |         for_each_impl<boost::is_same<iter,LastIterator>::value>  
>       |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>    82 |             ::execute( static_cast<iter*>(0), static_cast<LastIterator*>(0), static_cast<TransformFunc*>(0), f);  
>       |             ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/mpl/for_each.hpp:105:18:   required from 'void boost::mpl::for_each(F, Sequence*, TransformOp*) [with Sequence = s_item<State<2>, s_item<State1, set0<> > >; TransformOp = boost::msm::wrap<mpl_::arg<1> >; F = boost::msm::back::state_machine<Front>::init_flags<Flag1>]'  
>   104 |     aux::for_each_impl< boost::is_same<first,last>::value >  
>       |     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>   105 |         ::execute(static_cast<first*>(0), static_cast<last*>(0), static_cast<TransformOp*>(0), f);  
>       |         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/msm/back/state_machine.hpp:2386:29:   required from 'bool (** boost::msm::back::state_machine<A0, A1, A2, A3, A4>::get_entries_for_flag() const)(const library_sm&) [with Flag = Flag1; A0 = Front; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_; flag_handler = bool (*)(const boost::msm::back::state_machine<Front>&)]'  
>  2385 |             (::boost::mpl::for_each<state_list, boost::msm::wrap< ::boost::mpl::placeholders::_1> >  
>       |              ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
>  2386 |                             (init_flags<Flag>(flags_entries)),  
>       |                             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
> /app/boost/include/boost/msm/back/state_machine.hpp:1518:95:   required from 'bool boost::msm::back::state_machine<A0, A1, A2, A3, A4>::is_flag_active() const [with Flag = Flag1; A0 = Front; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_]'  
>  1518 |         return FlagHelper<Flag,(nr_regions::value>1)>::helper(*this,get_entries_for_flag<Flag>());  
>       |                                                                     ~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
> <source>:42:43:   required from here  
>    42 |     std::cout << fsm.is_flag_active<Flag1>();  
>       |                  ~~~~~~~~~~~~~~~~~~~~~~~~~^~  
> /app/boost/include/boost/mp11/detail/mpl_common.hpp:31:11: error: no type named 'type' in 'struct boost::mp11::detail::mp_pop_front_impl<boost::mp11::mp_list<> >'  
>    31 |     using next = mp11_iterator<mp11::mp_rest<L>>;  
>       |           ^~~~  
>   
  
  
The code:  
  
```cpp  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
#include <boost/msm/front/functor_row.hpp>  
#include <boost/mp11/list.hpp>  
#include <boost/mp11/mpl_list.hpp>  
  
#include <iostream>  
  
template <auto I> struct Event {};  
template <auto I> struct Action{  
    void operator()(auto&& ...) {}  
};  
template <int I>  
struct State: boost::msm::front::state<>   
{  
};  
struct Flag1{};  
struct State1 : State<1>  
{  
    using flag_list = boost::mp11::mp_list<Flag1>;  
    // all these definition below works - so WA is easy  
    // using flag_list = boost::mpl::vector<Flag1>;  
    // using flag_list = boost::mpl::vector1<Flag1>;  
    // using flag_list = boost::fusion::vector<Flag1>;  
};  
  
struct Front : boost::msm::front::state_machine_def<Front>   
{  
    struct transition_table : boost::mpl::vector<  
        boost::msm::front::Row<State1, Event<1>, State<2>, Action<12>>  
    > {};  
  
    using initial_state = boost::mpl::vector<State1>;  
};  
  
int main() {  
    using Fsm = boost::msm::back::state_machine<Front>;  
    Fsm fsm;  
      
    fsm.start();  
    std::cout << fsm.is_flag_active<Flag1>();  
}  
  
```
