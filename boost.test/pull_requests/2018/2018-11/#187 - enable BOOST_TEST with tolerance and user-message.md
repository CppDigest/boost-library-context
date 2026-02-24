# #187 enable BOOST_TEST with tolerance and user-message [Closed]

> Username: tobias-loew  
> Created at: 2018-11-14 14:16:27 UTC  
> Updated at: 2020-04-21 14:23:35 UTC  
> Closed at: 2020-04-08 17:14:45 UTC  
> Url: https://github.com/boostorg/test/pull/187  

the additional operator << allow using BOOST_TEST with tolerance and user-message, e.g   
  
BOOST_TEST(a == b, "a not equals b" << boost::test_tools::tolerance(0.000001));

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2019-11-06 07:54:36 UTC  
> Url: https://github.com/boostorg/test/pull/187#issuecomment-550190043  

Unfortunately the PR shows some regressions and it needs some work, this is why it has not been merged so far.

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2020-01-10 16:39:29 UTC  
> Url: https://github.com/boostorg/test/pull/187#issuecomment-573110309  

Can you give me a link to the test or the code that shows regressions? Thanks!

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2020-04-08 14:10:52 UTC  
> Url: https://github.com/boostorg/test/pull/187#issuecomment-610982955  

@tobias-loew This is being superseded by the branch `topic/PR-187-BOOST_TEST-with-tolerance-message`. It would be nice if you can give a try to see if this addresses your needs.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2020-04-08 17:14:45 UTC  
> Url: https://github.com/boostorg/test/pull/187#issuecomment-611082241  

Fix merged to master.

---
