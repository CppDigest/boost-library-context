# #36 - Constants should be policy-based [Open]

> Username: dcolascione  
> Created at: 2019-07-22 17:34:45 UTC  
> Updated at: 2023-02-05 16:07:03 UTC  
> Url: https://github.com/boostorg/sort/issues/36  

Right now, `boost/sort/spreadsort/detail/constants.hpp` defines a bunch of performance-tuning constants for spreadsort. Why make these constants global? Because these constants live in a simple enum, they're impossible to change without changing the Boost headers, which is awkward when compiling against a Boost installed system-wide.  
  
Instead, spreadsort should accept a struct containing these constants as a template argument. This way, users would be able to customize these parameters for specific invocations of spreadsort without changing the Boost headers themselves.

---

## Comment 1

> Username: spreadsort  
> Created at: 2019-09-22 15:30:23 UTC  
> Url: https://github.com/boostorg/sort/issues/36#issuecomment-533891624  

On Mon, Jul 22, 2019 at 1:34 PM Daniel Colascione <notifications@github.com>  
wrote:  
  
> Right now, boost/sort/spreadsort/detail/constants.hpp defines a bunch of  
> performance-tuning constants for spreadsort. Why make these constants  
> global? Because these constants live in a simple enum, they're impossible  
> to change without changing the Boost headers, which is awkward when  
> compiling against a Boost installed system-wide.  
>  
> Instead, spreadsort should accept a struct containing these constants as a  
> template argument. This way, users would be able to customize these  
> parameters for specific invocations of spreadsort without changing the  
> Boost headers themselves.  
>  
  
If you can put together a backwards-compatible (in terms of the public  
interface, so nobody needs to change their code) pull request with the  
change you're recommending, I'd be happy to review it.  
  
  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/36?email_source=notifications&email_token=ABG2HXBCCWQLDREQYIZRJGDQAXVTRA5CNFSM4IF22NK2YY3PNVWWK3TUL52HS4DFUVEXG43VMWVGG33NNVSW45C7NFSM4HAWF5HQ>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXGPOU5V65K5Q7MGC53QAXVTRANCNFSM4IF22NKQ>  
> .  
>

---

## Comment 2

> Username: spreadsort  
> Created at: 2023-02-05 02:32:37 UTC  
> Url: https://github.com/boostorg/sort/issues/36#issuecomment-1416904888  

What application were you thinking of customizing these parameters for?  When I wrote this library I included the constants under the assumption people would want just 1 set based on the type of computer they were running on, as data distributions can be unpredictable.  Is this software running on multiple very different types of computers (like a phone vs a server)?  If you're instead planning to heavily tune for the data distribution you might run into poor performance if the software runs into a new distribution.

---

## Comment 3

> Username: spreadsort  
> Created at: 2023-02-05 16:07:03 UTC  
> Url: https://github.com/boostorg/sort/issues/36#issuecomment-1418091697  

It looks like I can't do this with a struct and compile them in, which is critical for the optimization.  I'm also trying to maintain backwards compatibility with C++ 03.  
  
What could be possible would be passing them as additional template arguments (all 5 of them if necessary), maybe with a default value.  Would that meet your need?  What kind of benefit does this provide?  
  
Downside: your binary will get larger if you have multiple versions of spreadsort depending on various tunings, and that can slow it down.  each compiled copy of spreadsort has an array of length 2^max_finishing_splits on the stack.
