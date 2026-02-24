# #167 - backmp11: Incorrect FSM type in call on_entry() & on_exit() [Closed]

> Username: heretic13  
> Created at: 2026-01-30 12:57:02 UTC  
> Updated at: 2026-02-05 22:30:25 UTC  
> Closed at: 2026-02-05 22:30:25 UTC  
> Url: https://github.com/boostorg/msm/issues/167  

Hello.  
  
When using a sub-FSM, upon transitioning to its default state, on_entry() is called with an incorrect FSM type. It should match the sub-FSM type. However, it is called with the parent FSM (or root?).  
For back/back11, the behavior is correct.  
For backmp11, there is a difference.  
  
Example.

---

## Comment 1

> Username: heretic13  
> Created at: 2026-01-30 12:58:28 UTC  
> Updated at: 2026-01-30 16:06:45 UTC  
> Url: https://github.com/boostorg/msm/issues/167#issuecomment-3823629204  

```  
  
#include <fmt/printf.h>  
  
#include <boost/core/demangle.hpp>  
  
////#define USE_MSM_BACK  
//#define USE_MSM_BACK11  
#define USE_MSM_BACK_MP11  
  
#if defined(USE_MSM_BACK)  
  
#include <boost/msm/back/state_machine.hpp>  
  
#elif defined(USE_MSM_BACK11)  
  
#include <boost/msm/back11/state_machine.hpp>  
  
#elif defined(USE_MSM_BACK_MP11)  
  
  
#include <boost/msm/backmp11/state_machine.hpp>  
#include <boost/mp11/list.hpp>  
  
#endif  
  
  
//front-end  
#include <boost/msm/front/state_machine_def.hpp>  
  
// functors  
#include <boost/msm/front/functor_row.hpp>  
  
namespace msm = boost::msm;  
namespace mpl = boost::mpl;  
namespace mp11 = boost::mp11;  
namespace msmf = msm::front;  
  
struct EvSwt2DirectPlay  
{  
};  
  
struct EvCancel {};  
  
struct EvDirectSound  
{  
};  
  
// front-end: define the FSM structure  
struct Playing_ : public msm::front::state_machine_def<Playing_>  
{  
	template <class Event, class FSM>  
	void on_entry(Event const&, FSM& fsm)  
	{  
		fmt::print("enter Playing. fsm={}\n", boost::core::demangle(typeid(fsm).name()) );  
	}  
  
	template <class Event, class FSM>  
	void on_exit(Event const&, FSM& fsm)  
	{  
		fmt::print("exit Playing\n");  
	}  
  
	// Replaces the default no-transition response.  
	template <class FSM, class Event>  
	void no_transition(Event const& e, FSM& fsm, int state)  
	{  
		fmt::print("Playing no_transition. state={}, event={}\n", state, boost::core::demangle(typeid(e).name()));  
	}  
  
	struct BaseState : public msm::front::state<>  
	{  
		// every (optional) entry/exit methods get the event passed.  
		template <class Event, class FSM>  
		void on_entry(Event const&, FSM& fsm)  
		{  
			fmt::print("enter Playing::BaseState. fsm={}\n", boost::core::demangle(typeid(fsm).name()) );  
		}  
  
		template <class Event, class FSM>  
		void on_exit(Event const&, FSM& fsm)  
		{  
			fmt::print("exit Playing::BaseState. fsm={}\n", boost::core::demangle(typeid(fsm).name()) );  
		}  
	};  
  
	// the initial state of the player SM. Must be defined  
	typedef BaseState initial_state;  
  
	// transition actions  
  
	// guard conditions  
  
	// Transition table for Playing  
#if defined(USE_MSM_BACK) || defined(USE_MSM_BACK11)  
	using transition_table = mpl::vector <  
#elif defined(USE_MSM_BACK_MP11)  
	using transition_table = mp11::mp_list<  
#endif  
		  
	>;  
};  
  
// Pick a back-end  
#if defined(USE_MSM_BACK)  
typedef msm::back::state_machine<Playing_> Playing;  
#elif defined(USE_MSM_BACK11)  
typedef msm::back11::state_machine<Playing_> Playing;  
#elif defined(USE_MSM_BACK_MP11)  
using Playing=msm::backmp11::state_machine<Playing_>;  
#endif  
  
  
// front-end: define the FSM structure  
struct Logic_ : public msm::front::state_machine_def<Logic_>  
{  
	Logic_()  
	{  
	}  
  
	template <class Event, class FSM>  
	void on_entry(Event const&, FSM& fsm)  
	{  
		fmt::print("enter Logic\n");  
	}  
  
	template <class Event, class FSM>  
	void on_exit(Event const&, FSM& fsm)  
	{  
		fmt::print("exit Logic\n");  
	}  
  
	// Replaces the default no-transition response.  
	template <class FSM, class Event>  
	void no_transition(Event const& e, FSM& fsm, int state)  
	{  
		fmt::print("Logic no_transition. state={}, event={}\n", state, boost::core::demangle(typeid(e).name()));  
	}  
  
	// The list of FSM states  
  
	struct IdleState : public msm::front::state<>  
	{  
		// every (optional) entry/exit methods get the event passed.  
		template <class Event, class FSM>  
		void on_entry(Event const&, FSM& fsm)  
		{  
			fmt::print("enter Logic::IdleState\n");  
		}  
  
		template <class Event, class FSM>  
		void on_exit(Event const&, FSM& fsm)  
		{  
			fmt::print("exit Logic::IdleState\n");  
		}  
	};  
  
	// the initial state of the player SM. Must be defined  
	typedef IdleState initial_state;  
  
	// transition actions  
  
	// guard conditions  
  
	// Transition table for Logic  
#if defined(USE_MSM_BACK) || defined(USE_MSM_BACK11)  
	using transition_table = mpl::vector <  
#elif defined(USE_MSM_BACK_MP11)  
	using transition_table = mp11::mp_list <  
#endif  
		//		        Start				Event						Next					Action							Guard  
		//	+----------------------------+---------------------------+-----------------------+------------------------------+---------------------------+  
		msmf::Row <IdleState,				EvSwt2DirectPlay,			Playing,				msmf::none,						msmf::none				>,  
  
		msmf::Row <IdleState, EvCancel, msmf::none, msmf::none, msmf::none				>,  
		msmf::Row <IdleState, EvDirectSound, msmf::none, msmf::none, msmf::none				>,  
		//  +----------------------------+---------------------------+-----------------------+------------------------------+---------------------------+  
  
		msmf::Row <Playing, EvCancel, IdleState, msmf::none, msmf::none				>,  
  
		msmf::Row <Playing, EvSwt2DirectPlay, msmf::none, msmf::none, msmf::none				>,  
  
		msmf::Row <IdleState, EvDirectSound, msmf::none, msmf::none, msmf::none				>  
		//  +----------------------------+---------------------------+-----------------------+------------------------------+---------------------------+  
	> ;  
};  
  
#if defined(USE_MSM_BACK)  
typedef msm::back::state_machine<Logic_> Logic;  
#elif defined(USE_MSM_BACK11)  
typedef msm::back11::state_machine<Logic_> Logic;  
#elif defined(USE_MSM_BACK_MP11)  
using Logic=msm::backmp11::state_machine<Logic_>;  
#endif  
  
int main()  
{  
	Logic l;  
  
	l.start();  
  
	l.process_event(EvSwt2DirectPlay{});  
  
	return 0;  
}  
  
```

---

## Comment 2

> Username: chandryan  
> Created at: 2026-01-30 19:06:13 UTC  
> Updated at: 2026-01-30 19:18:18 UTC  
> Url: https://github.com/boostorg/msm/issues/167#issuecomment-3825228113  

Hi,  
  
thanks for providing the example code. For reference, running it yields the following exemplary output:  
  
```  
backmp11:  
enter Logic  
enter Logic::IdleState  
exit Logic::IdleState  
enter Playing. fsm=boost::msm::backmp11::state_machine<Logic_>  
enter Playing::BaseState. fsm=boost::msm::backmp11::state_machine<Logic_>  
  
back:  
enter Logic  
enter Logic::IdleState  
exit Logic::IdleState  
enter Playing. fsm=boost::msm::back::state_machine<Logic_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>  
enter Playing::BaseState. fsm=boost::msm::back::state_machine<Playing_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_, boost::parameter::void_>  
```  
  
In backmp11 the default setting for the fsm parameter is currently `transition_owner`. In UML I could find the following reference:  
  
> The owner of a Transition is not explicitly constrained, though the Region in which it is contained must be owned  
directly or indirectly by the owning StateMachine. A suggested owner of a Transition is the innermost Region that  
contains both its source and target Vertices.  
  
I initially thought that the owner of the transition into the target SM `Playing` would be the parent SM `Logic`. But that doesn't align with the UML reference, in fact there are probably two transitions involved:  
  
- 1: From `Logic::IdleState` to `Playing` (or better `Playing::<initial_pseudostate>`?), the owner is `Logic` (as seen in the MSM code)  
- 2: From `Playing::<initial_pseudostate>` to `Playing::BaseState`, the owner is `Playing` (not visible in the MSM code, but at least conceptually this transition should exist)  
  
I need to spend some more time to dig deeper and cross-check, though I think you are correct:  
The fsm argument for `Playing::BaseState`'s entry method should be `state_machine<Playing_>`.

---

## Comment 3

> Username: chandryan  
> Created at: 2026-02-05 15:51:03 UTC  
> Url: https://github.com/boostorg/msm/issues/167#issuecomment-3854524365  

I merged a fix to MSM's develop branch, to be available in the next Boost 1.91 release. For `backmp11` your example now prints:  
  
```  
enter Logic  
enter Logic::IdleState  
exit Logic::IdleState  
enter Playing. fsm=boost::msm::backmp11::state_machine<Logic_>  
enter Playing::BaseState. fsm=boost::msm::backmp11::state_machine<Playing_>  
```  
  
The call to `on_exit()` was also affected and should yield the same Fsm parameter now as in the older back-ends.  
  
I realized the term "transition owner" is not correct to describe what `back/back11` implement and renamed it to "local transition owner". I hope that makes the Fsm setting more understandable and leaves room for a correct "transition owner" setting in the future, if needed.  
  
Would you like to cross-check @heretic13 or can we close this issue as completed?

---

## Comment 4

> Username: heretic13  
> Created at: 2026-02-05 16:08:54 UTC  
> Url: https://github.com/boostorg/msm/issues/167#issuecomment-3854630136  

I'll take your word for it.  
Pulling the development branch and checking it out is too much work for me.
