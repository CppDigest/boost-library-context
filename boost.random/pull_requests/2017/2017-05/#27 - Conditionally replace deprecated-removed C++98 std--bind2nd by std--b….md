# #27 Conditionally replace deprecated/removed C++98 std::bind2nd by std::b… [Merged]

> Username: DanielaE  
> Created at: 2017-05-13 13:32:02 UTC  
> Updated at: 2018-01-14 16:37:28 UTC  
> Merged at: 2017-11-06 21:46:46 UTC  
> Closed at: 2017-11-06 21:46:46 UTC  
> Url: https://github.com/boostorg/random/pull/27  

…ind, and std::random_shuffle by an adapted std::shuffle.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-05-13 14:15:51 UTC  
> Url: https://github.com/boostorg/random/pull/27#issuecomment-301251044  

AMDG  
  
On 05/13/2017 07:32 AM, Daniela Engert wrote:  
> …ind, and std::random_shuffle by an adapted std::shuffle.  
>   
> Signed-off-by: Daniela Engert <dani@ngrt.de>  
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/random/pull/27  
>   
> -- Commit Summary --  
>   
>   * Conditionally replace deprecated/removed C++98 std::bind2nd by std::bind, and std::random_shuffle by an adapted std::shuffle.  
>   
> -- File Changes --  
>   
>     M include/boost/random/uniform_on_sphere.hpp (4)  
  
  It would be better to use a loop or write  
a custom function object.  
  
>     M test/test_old_uniform_int_distribution.cpp (39)  
>     M test/test_random_number_generator.cpp (38)  
>   
  
  Adapting these to use std::shuffle makes no sense,  
because they are specifically testing compatibility  
with std::random_shuffle.  If std::random_shuffle  
doesn't exist, there's no point to these tests and  
they can just be #ifdef'ed out entirely.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-05-13 15:42:12 UTC  
> Url: https://github.com/boostorg/random/pull/27#issuecomment-301256113  

> > M test/test_old_uniform_int_distribution.cpp (39)  
> > M test/test_random_number_generator.cpp (38)  
> >  
>  
> Adapting these to use std::shuffle makes no sense,  
> because they are specifically testing compatibility  
> with std::random_shuffle. If std::random_shuffle  
> doesn't exist, there's no point to these tests and  
> they can just be #ifdef'ed out entirely.  
>  
  
FYI we have |BOOST_NO_CXX98_RANDOM_SHUFFLE for just that purpose.  
  
HTH, John.  
|  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-05-13 17:04:02 UTC  
> Url: https://github.com/boostorg/random/pull/27#issuecomment-301260905  

Steve, I hope you like this more!  
John, of course ...

---

## Comment 4

> Username: swatanabe  
> Created_at: 2017-05-13 17:13:53 UTC  
> Url: https://github.com/boostorg/random/pull/27#issuecomment-301261490  

AMDG  
  
On 05/13/2017 11:04 AM, Daniela Engert wrote:  
> Steve, I hope you like this more!  
> John, of course ...  
>   
  
  The random_shuffle changes look good, but  
in C++03, a local class cannot be a template  
parameter.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: DanielaE  
> Created_at: 2017-05-13 17:26:39 UTC  
> Url: https://github.com/boostorg/random/pull/27#issuecomment-301262266  

Steve, thx for this info - never heard about that before. Then I am probably going to make Sean unhappy ...

---
