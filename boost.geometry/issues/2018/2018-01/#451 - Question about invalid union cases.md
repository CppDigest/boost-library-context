# #451 - Question about invalid union cases [Closed]

> Username: lukegluke  
> Created at: 2018-01-16 09:27:28 UTC  
> Updated at: 2018-04-09 13:07:14 UTC  
> Closed at: 2018-04-09 13:07:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/451  

Hi,  
  
In test/algorithms/overlay/multi_overlay_cases.hpp I see comment:  
  
// Union was reported as invalid because of no generated interior rings, **fixed**  
for cases ticket_11984, ticket_12118, ticket_12125, ticket_12751, ticket_12752  
  
Does this mean that these tickets are solved? If so why they are not closed (except 11984) in https://svn.boost.org/trac10 (is this trac system still alive)?  
  
Thanks in advance for explanations.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2018-02-14 16:42:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/451#issuecomment-365667769  

Thanks for your question.  
Yes, the trac system is still used. I'm still busy with enhancements for union/intersection and this also influences those tickets. Will verify and close them afterwards, this can still take a while.
