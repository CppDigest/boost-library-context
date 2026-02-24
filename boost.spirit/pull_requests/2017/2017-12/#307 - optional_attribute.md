# #307 optional_attribute [Merged]

> Username: octopus-prime  
> Created at: 2017-12-01 18:13:04 UTC  
> Updated at: 2017-12-07 10:41:48 UTC  
> Merged at: 2017-12-04 10:37:52 UTC  
> Closed at: 2017-12-04 10:37:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/307  

PR for issue #250 "optional_attribute is useless in x3/support/traits/attribute_category.hpp"

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-02 04:45:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/307#issuecomment-348668057  

Do you think we need a ticket for this with better description of the problem?

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-12-02 12:02:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/307#issuecomment-348687597  

> Do you think we need a ticket for this with better description of the problem?  
  
The ticket is here: #250  
If there is a real problem?! I don't know. Tests are passing with and without patch...  
So currently it seams to be just more code.  
But later it might help to work on specialized optional stuff.  
Or will work on optional stuff become harder because of the specialized code??!  
  
> Unlike the other *_attribute tags, optional_attribute is not used anywhere.  
  
Currently that's the only relevant point. There is `optional_attribute` without definition/usage...  
  
> Hmmmm.. seems you are right. If you have a patch we can test if the test-suite still passes.  
  
So here is the added patch and tests are passing. Currently that's all.  
So the team should discuss about the value/sense of this PR. I am unsure...

---

## Comment 3

> Username: djowel  
> Created_at: 2017-12-02 21:54:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/307#issuecomment-348722889  

right, i got confused. sorry, i am blind :P. please go ahead.

---
