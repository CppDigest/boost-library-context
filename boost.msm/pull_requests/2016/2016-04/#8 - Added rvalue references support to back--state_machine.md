# #8 Added rvalue references support to back::state_machine. [Merged]

> Username: redboltz  
> Created at: 2016-04-11 03:36:32 UTC  
> Updated at: 2017-12-21 14:16:00 UTC  
> Merged at: 2017-12-21 13:01:23 UTC  
> Closed at: 2017-12-21 13:01:23 UTC  
> Url: https://github.com/boostorg/msm/pull/8  

I'd like to use a class that inherits state_machine_def and contains move only type member variables as follows:  
  
``` C++  
#include <boost/msm/back/state_machine.hpp>  
#include <boost/msm/front/state_machine_def.hpp>  
  
namespace msm = boost::msm;  
  
struct foo {  
    foo() = default;  
    foo(foo const&) = delete;  
    foo(foo&&) = default;  
};  
  
// ----- State machine  
  
struct Sm1_:msm::front::state_machine_def<Sm1_> {  
    struct State1:msm::front::state<> {};  
    typedef State1 initial_state;  
    Sm1_(foo&& val):val(std::move(val)) {}  
    foo val;  
};  
  
  
typedef msm::back::state_machine<Sm1_> Sm1;  
  
int main() {  
    foo f;  
    Sm1 sm1(std::move(f));  
}  
```  
  
However, `msm::back::state_machine` cannot forward the arguments correctly. So I wrote the pull request that solves this issue.

---

## Review 1 [Approved]

> Username: henry-ch  
> Created_at: 2017-12-21 13:01:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/msm/pull/8#pullrequestreview-85054334  

Looks good and passes tests

---

## Comment 2

> Username: redboltz  
> Created_at: 2017-12-21 13:09:01 UTC  
> Url: https://github.com/boostorg/msm/pull/8#issuecomment-353346651  

Thank you!!

---

## Comment 3

> Username: henry-ch  
> Created_at: 2017-12-21 14:16:00 UTC  
> Url: https://github.com/boostorg/msm/pull/8#issuecomment-353361310  

You did all the work. I only reviewed.  
Sorry for the very long delay!

---
