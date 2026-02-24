# #24 - Assign support for std::array [Closed]

> Username: jeking3  
> Created at: 2018-10-13 14:36:30 UTC  
> Updated at: 2018-10-13 15:15:42 UTC  
> Closed at: 2018-10-13 15:04:32 UTC  
> Url: https://github.com/boostorg/assign/issues/24  

Moving ticket from trac to github: https://svn.boost.org/trac10/ticket/8238  
  
For compilers that support std::array, can support be added to boost::assign for std::array at least with the same capabilities matching boost::array?  
  
For example, list_of works with boost::array, but not std::array.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-10-13 14:39:38 UTC  
> Url: https://github.com/boostorg/assign/issues/24#issuecomment-429547347  

AMDG  
  
On 10/13/2018 08:36 AM, James E. King III wrote:  
> Moving ticket from trac to github: https://svn.boost.org/trac10/ticket/8238  
>   
> For compilers that support std::array, can support be added to boost::assign for std::array at least with the same capabilities matching boost::array?  
>   
> For example, list_of works with boost::array, but not std::array.  
>   
  
Why are we spending so much effort on Boost.Assign?  
It's obsolete in C++11, because of initializer lists.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-13 15:04:32 UTC  
> Url: https://github.com/boostorg/assign/issues/24#issuecomment-429549104  

Just going through the motions of cleaning up the backlog.  
Due to the sheer number of issues in the CMT repositories  
(mostly stuck in trac) when I don't have a simple answer to   
something, I'd rather get an opinion on it.  This keeps experts  
from having to deal with mundane backlog cleanup activities   
and at the same time helps educate myself and others, so   
everyone wins.  
  
I agree with you given the recent discussions about C++03  
support on the mailing list.  I'm happy to close something  
like this as "wontfix" with these reasons, but it's nice to close  
the loop with folks who took the time to submit an issue in  
the first place.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-10-13 15:15:42 UTC  
> Url: https://github.com/boostorg/assign/issues/24#issuecomment-429549897  

AMDG  
  
On 10/13/2018 09:04 AM, James E. King III wrote:  
> Just going through the motions of cleaning up the backlog.  
> Due to the sheer number of issues in the CMT repositories  
> (mostly stuck in trac) when I don't have a simple answer to   
> something, I'd rather get an opinion on it.  This keeps experts  
> from having to deal with mundane backlog cleanup activities   
> and at the same time helps educate myself and others, so   
> everyone wins.  
>   
> I agree with you given the recent discussions about C++03  
> support on the mailing list.  I'm happy to close something  
> like this as "wontfix" with these reasons, but it's nice to close  
> the loop with folks who took the time to submit an issue in  
> the first place.  
>   
  
It depends on the ticket.  std::array is a C++11  
feature, so I don't see any point in adding support  
for it.  In #22, on the other hand, std::list was  
always supposed to work, so the failure is a regression.  
  
In Christ,  
Steven Watanabe
