# #35 - Compile failure when usign togerther with range/algorithm [Open]

> Username: RegexPuzzle  
> Created at: 2019-06-28 12:34:48 UTC  
> Updated at: 2019-09-22 15:27:56 UTC  
> Url: https://github.com/boostorg/sort/issues/35  

The following code fails to compile:  
  
#include <boost/range/algorithm.hpp>  
#include <boost/sort/sort.hpp>  
void main(){}

---

## Comment 1

> Username: spreadsort  
> Created at: 2019-09-22 15:27:56 UTC  
> Url: https://github.com/boostorg/sort/issues/35#issuecomment-533891457  

Yes, that's due to a known scoping problem in boost/range/algorithm  
  
On Fri, Jun 28, 2019 at 8:34 AM RegexPuzzle <notifications@github.com>  
wrote:  
  
> The following code fails to compile:  
>  
> #include <boost/range/algorithm.hpp>  
> #include <boost/sort/sort.hpp>  
> void main(){}  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/35?email_source=notifications&email_token=ABG2HXCYFFZBSQGSXSBCYITP4YAOTA5CNFSM4H4E7E2KYY3PNVWWK3TUL52HS4DFUVEXG43VMWVGG33NNVSW45C7NFSM4G4JYRJQ>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXB7JMCT5OKAP56NLGTP4YAOTANCNFSM4H4E7E2A>  
> .  
>
