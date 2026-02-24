# #22 - pointer used after 'void operator delete(void*, std::size_t)'  in simple_state.hpp [Open]

> Username: oliver124810  
> Created at: 2022-09-07 18:37:10 UTC  
> Updated at: 2022-09-19 05:57:04 UTC  
> Url: https://github.com/boostorg/statechart/issues/22  

Switching recently from GCC 11 to 12 (MinGW), we encountered the following new warning:  
  
```  
inlined from 'boost::statechart::detail::reaction_result boost::statechart::simple_state<MostDerived, Context, InnerInitial, historyMode>::react_impl(const event_base_type&, typename Context::inner_context_type::rtti_policy_type::id_type) [with MostDerived = ourstuff::{anonymous}::OurStuff Context = ourstuff::{anonymous}::OurStuff; InnerInitial = boost::mpl::list<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>; boost::statechart::history_mode historyMode = boost::statechart::has_no_history]' at C:/.../boost-1_80/boost/statechart/simple_state.hpp:489:37:  
  
C:/.../boost-1_80/boost/statechart/detail/counted_base.hpp:80:27: error: pointer used after 'void operator delete(void*, std::size_t)' [-Werror=use-after-free]  
  
   80 |       return --base_type::count_ == 0;  
```  
  
This happens only in the "RelWithDebInfo" CMake configuration.  
  
As a workaround, we patched this file to disable the warning using a #pragma, since warnings are considered as errors in this project.  
  
This is the code producing the warning in context, for reference:  
```  
    bool release() const  
    {  
      BOOST_ASSERT( base_type::count_ > 0 );  
      return --base_type::count_ == 0;  
    }  
```

---

## Comment 1

> Username: mclow  
> Created at: 2022-09-19 01:48:15 UTC  
> Url: https://github.com/boostorg/statechart/issues/22#issuecomment-1250461594  

The cause of that warning is not obvious to me.  
The (long) error message refers to stuff named `OurStuff`, which is not anywhere in the boost sources.

---

## Comment 2

> Username: oliver124810  
> Created at: 2022-09-19 05:30:34 UTC  
> Url: https://github.com/boostorg/statechart/issues/22#issuecomment-1250587024  

OurStuff belongs to the application code. I do not know how to reproduce the issue without it, unfortunately.

---

## Comment 3

> Username: mclow  
> Created at: 2022-09-19 05:33:41 UTC  
> Url: https://github.com/boostorg/statechart/issues/22#issuecomment-1250588586  

In that case, why do you think that this is a bug in Boost.Statechart, and not in your application?

---

## Comment 4

> Username: oliver124810  
> Created at: 2022-09-19 05:43:42 UTC  
> Updated at: 2022-09-19 05:44:07 UTC  
> Url: https://github.com/boostorg/statechart/issues/22#issuecomment-1250593150  

I do not know indeed, the only understandable and traceable hint I have is the error message pointing to counted_base.hpp:80  
If you do not know what is causing this either, you can close the ticket. Our workaround is good enough for us.

---

## Comment 5

> Username: mclow  
> Created at: 2022-09-19 05:52:11 UTC  
> Url: https://github.com/boostorg/statechart/issues/22#issuecomment-1250596637  

If there's a problem in the Boost.Statechart code, I'd like to find it and fix it.  
However, I don't think there's enough information in what you've posted for me to tell if that's the case.

---

## Comment 6

> Username: mclow  
> Created at: 2022-09-19 05:57:04 UTC  
> Url: https://github.com/boostorg/statechart/issues/22#issuecomment-1250598614  

Error message formatted:  
  
```  
inlined from   
boost::statechart::detail::reaction_result  
   boost::statechart::simple_state<MostDerived, Context, InnerInitial, historyMode>::react_impl(  
     const event_base_type&, typename Context::inner_context_type::rtti_policy_type::id_type) [  
        with MostDerived = ourstuff::{anonymous}::OurStuff Context = ourstuff::{anonymous}::OurStuff;   
             InnerInitial = boost::mpl::list<mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>;  
             boost::statechart::history_mode historyMode = boost::statechart::has_no_history]  
at C:/.../boost-1_80/boost/statechart/simple_state.hpp:489:37:  
  
C:/.../boost-1_80/boost/statechart/detail/counted_base.hpp:80:27:  
    error: pointer used after 'void operator delete(void*, std::size_t)' [-Werror=use-after-free]  
```
