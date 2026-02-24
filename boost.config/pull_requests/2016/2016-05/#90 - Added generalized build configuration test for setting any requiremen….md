# #90 Added generalized build configuration test for setting any requiremen… [Closed]

> Username: eldiener  
> Created at: 2016-05-25 18:20:28 UTC  
> Updated at: 2016-05-29 01:18:29 UTC  
> Closed at: 2016-05-29 01:18:29 UTC  
> Url: https://github.com/boostorg/config/pull/90  

…t when true or false and changed the 'requires' test to work in terms of the generalized test. I have not documented the generalized test but am willing to do so. Also I do not know if there is any Boost.config test for 'requires' so I have not attempted to test the changed functionality in a formal Boost.config test, although I have tested it out from another library on which I am working to ascertain that 'requires' and the new 'requires.set.properties' rules work accordingly.

---

## Comment 1

> Username: eldiener  
> Created_at: 2016-05-29 01:18:29 UTC  
> Url: https://github.com/boostorg/config/pull/90#issuecomment-222337250  

I realize that this does not work properly with multiple targets setting iftrue and iffalse requirements, and therefore I am going to close this PR.

---
