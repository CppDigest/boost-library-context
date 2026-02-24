# #1 to_int also should be called with this-> [Closed]

> Username: dpantele  
> Created at: 2014-04-13 19:15:11 UTC  
> Updated at: 2015-01-12 18:18:04 UTC  
> Closed at: 2015-01-09 19:01:46 UTC  
> Url: https://github.com/boostorg/interval/pull/1  

When I tried to build new policies base on existing, I've got problems with gcc which are fixed by adding this->  
  
This change it not tested, but I can't see how it can break something.

---

## Comment 1

> Username: v4hn  
> Created_at: 2015-01-09 16:52:53 UTC  
> Url: https://github.com/boostorg/interval/pull/1#issuecomment-69362357  

Is there a reason why this hasn't already been merged?  
I just hit this bug as well and it's pretty annoying.

---

## Comment 2

> Username: mclow  
> Created_at: 2015-01-09 18:01:01 UTC  
> Url: https://github.com/boostorg/interval/pull/1#issuecomment-69372422  

Well, I haven't merged it because this is the first I've seen it. However, if this was brand new, I would say "We don't merge into master - we merge into develop, let the tests cycle, and then merge from develop into master.".  
  
However, I'll be happy to manually apply this today.

---

## Comment 3

> Username: mclow  
> Created_at: 2015-01-09 18:01:24 UTC  
> Url: https://github.com/boostorg/interval/pull/1#issuecomment-69372475  

Or rather "we pull into develop", and then merge into master.

---

## Comment 4

> Username: v4hn  
> Created_at: 2015-01-09 18:15:14 UTC  
> Url: https://github.com/boostorg/interval/pull/1#issuecomment-69374433  

On Fri, Jan 09, 2015 at 10:01:02AM -0800, Marshall Clow wrote:  
  
> We don't merge into master - we merge into develop, let the tests cycle, and then merge from develop into master.  
  
I wouldn't expect anything else from a boost module :-)  
  
> However, I'll be happy to manually apply this today.  
  
Thanks a lot!

---

## Comment 5

> Username: mclow  
> Created_at: 2015-01-09 19:01:46 UTC  
> Url: https://github.com/boostorg/interval/pull/1#issuecomment-69381445  

Applied to develop. I'd appreciate it if you would watch the tests and let me know if you see any problems. Barring problems, I'll merge this to master early next week.

---

## Comment 6

> Username: v4hn  
> Created_at: 2015-01-12 18:18:03 UTC  
> Url: https://github.com/boostorg/interval/pull/1#issuecomment-69617223  

As far as I can see there are no failing test cases due to this change.

---
