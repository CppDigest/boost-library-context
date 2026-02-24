# #1241 Update geometric.hpp [Closed]

> Username: kenarab  
> Created at: 2025-01-30 00:16:11 UTC  
> Updated at: 2025-01-30 18:51:21 UTC  
> Closed at: 2025-01-30 18:51:21 UTC  
> Url: https://github.com/boostorg/math/pull/1241  

Missing namespace in log  
  
std::log should be used because compilation fails on macOS.  
  
I made the same PR on Apr-2024  
  
https://github.com/boostorg/math/pull/1127

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2025-01-30 09:33:44 UTC  
> Url: https://github.com/boostorg/math/pull/1241#issuecomment-2623970558  

No there's something else going on here and this is not the correct fix: we want/need log to be found via ADL for user defined numberic types, and the BOOST_MATH_STD_USING macro at the start of the function adds a `using std::log;` declaration which should take care of things.  The question is why not?

---

## Comment 2

> Username: mborland  
> Created_at: 2025-01-30 13:32:38 UTC  
> Url: https://github.com/boostorg/math/pull/1241#issuecomment-2624529084  

@kenarab Do you have a reproducer or system specifications? I just ran current develop on an macOS ARM machine with clang and gcc and everything works fine. We also have a variety of Mac systems in our CI.

---

## Comment 3

> Username: kenarab  
> Created_at: 2025-01-30 15:05:57 UTC  
> Url: https://github.com/boostorg/math/pull/1241#issuecomment-2624750836  

Hi Matt!  
  
In this case is not in macOS ARM but a Linux Box.  
  
uname -a  
Linux 6.8.0-52-generic #53-Ubuntu SMP PREEMPT_DYNAMIC Sat Jan 11 00:06:25  
UTC 2025 x86_64 x86_64 x86_64 GNU/Linux  
  
lsb_release -a  
No LSB modules are available.  
Distributor ID: Ubuntu  
Description:    Ubuntu 24.04.1 LTS  
Release:        24.04  
Codename:       noble  
  
  
On Thu, Jan 30, 2025 at 10:32 AM Matt Borland ***@***.***>  
wrote:  
  
> @kenarab <https://github.com/kenarab> Do you have a reproducer or system  
> specifications? I just ran current develop on an macOS ARM machine with  
> clang and gcc and everything works fine. We also have a variety of Mac  
> systems in our CI.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/math/pull/1241#issuecomment-2624529084>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAWMBVUOXQBNCGZKRU4FJ5T2NISYXAVCNFSM6AAAAABWECEHM6VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDMMRUGUZDSMBYGQ>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>  
  
  
--   
 alejandro baranek  
@ken4rab <https://twitter.com/ken4rab>  
qbotics <http://qbotics.tumblr.com/> | rpolyhedra  
<https://qbotics.shinyapps.io/rpolyhedra-explorer/?_inputs_&polyhedron_color=%22%23FF0000FF%22&polyhedron_name=%22Triakis%20Tetrahedron%22&polyhedron_source=%22dmccooey%22&show_axes=false>  
| surferinvaders <http://surferinvaders.tumblr.com> | algebraic-soundscapes  
<http://imaginary.org/content/algebraic-soundscapes> | surfer-shuffle  
<http://imaginary.org/program/surfer-shuffle>

---

## Comment 4

> Username: mborland  
> Created_at: 2025-01-30 15:16:52 UTC  
> Url: https://github.com/boostorg/math/pull/1241#issuecomment-2624781233  

We are running almost the same configuration in the Drone CI without issue:  
  
Linux 371a6aac9be7 6.8.0-1008-aws #8-Ubuntu SMP Sat Apr 20 00:46:25 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux.  
  
Are you testing against current develop, or boost from ubuntu's repositories? Your patch from the last PR is only present in 1.86+, and ubuntu 24.04 is providing 1.83: https://packages.ubuntu.com/noble/libboost-dev

---

## Comment 5

> Username: kenarab  
> Created_at: 2025-01-30 15:23:33 UTC  
> Url: https://github.com/boostorg/math/pull/1241#issuecomment-2624799287  

Ok, the pipeline lifecycle is slow for having the PR with Ubuntu LTS. I  
thought it was already in production, no problem.  
  
I'm using liboost-dev but not the develop version but ubuntu's default. I  
will use the the dev version so I can check quickly the PR or anything  
reducing lifecycle. In brew MacOS I have installed by default boost-1.87.  
  
  
Thank you and we can close this PR if it is already solved.  
  
On Thu, Jan 30, 2025 at 12:17 PM Matt Borland ***@***.***>  
wrote:  
  
> We are running almost the same configuration in the Drone CI without issue:  
>  
> Linux 371a6aac9be7 6.8.0-1008-aws #8  
> <https://github.com/boostorg/math/pull/8>-Ubuntu SMP Sat Apr 20 00:46:25  
> UTC 2024 x86_64 x86_64 x86_64 GNU/Linux.  
>  
> Are you testing against current develop, or boost from ubuntu's  
> repositories? Your patch from the last PR is only present in 1.86+, and  
> ubuntu 24.04 is providing 1.83:  
> https://packages.ubuntu.com/noble/libboost-dev  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/math/pull/1241#issuecomment-2624781233>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAWMBVUECJMC546XOPDDIF32NI67VAVCNFSM6AAAAABWECEHM6VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDMMRUG44DCMRTGM>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>  
  
  
--   
 alejandro baranek  
@ken4rab <https://twitter.com/ken4rab>  
qbotics <http://qbotics.tumblr.com/> | rpolyhedra  
<https://qbotics.shinyapps.io/rpolyhedra-explorer/?_inputs_&polyhedron_color=%22%23FF0000FF%22&polyhedron_name=%22Triakis%20Tetrahedron%22&polyhedron_source=%22dmccooey%22&show_axes=false>  
| surferinvaders <http://surferinvaders.tumblr.com> | algebraic-soundscapes  
<http://imaginary.org/content/algebraic-soundscapes> | surfer-shuffle  
<http://imaginary.org/program/surfer-shuffle>

---
