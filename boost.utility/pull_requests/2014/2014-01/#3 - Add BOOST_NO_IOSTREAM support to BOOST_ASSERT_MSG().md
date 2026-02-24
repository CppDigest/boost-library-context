# #3 Add BOOST_NO_IOSTREAM support to BOOST_ASSERT_MSG(). [Closed]

> Username: k-satoda  
> Created at: 2014-01-04 07:03:15 UTC  
> Updated at: 2014-08-27 18:18:32 UTC  
> Closed at: 2014-08-27 18:18:32 UTC  
> Url: https://github.com/boostorg/utility/pull/3  

And introduce BOOST_ASSERT_MSG_FILE to configure the output stream like  
BOOST_ASSERT_MSG_OSTREAM.  
  
Use of iostream can be an obstacle to use libraries which uses  
BOOST_ASSERT_MSG() on some poor (about hardware, compiler, etc)  
environments.

---

## Comment 1

> Username: k-satoda  
> Created_at: 2014-01-04 07:12:31 UTC  
> Url: https://github.com/boostorg/utility/pull/3#issuecomment-31573280  

(Some additional notes)  
  
This can be a solution for my environmental problem. There was an  
alternative solution proposed, but rejected (closed as won't fix).  
https://svn.boost.org/trac/boost/ticket/7028  
  
I couldn't figure out how to write tests for this. Some other  
BOOST_NO_IOSTREAM support doesn't have tests, too. Moreover, there is  
no test for BOOST_ASSERT_MSG_OSTREAM. Then I made this pull request  
without adding test. If tests are required, please instruct me how to  
write tests for these features.  
  
If I should create a new ticket on Trac, I will do.

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-01-04 17:05:20 UTC  
> Url: https://github.com/boostorg/utility/pull/3#issuecomment-31583063  

> If I should create a new ticket on Trac, I will do.  
  
Yes please. Assert is in the process of being refactored into its own library/repository; once this occurs, I will take care of your request (I also plan to address #7028).

---

## Comment 3

> Username: k-satoda  
> Created_at: 2014-01-04 17:48:39 UTC  
> Url: https://github.com/boostorg/utility/pull/3#issuecomment-31584074  

> > If I should create a new ticket on Trac, I will do.  
>   
> Yes please. ...  
  
OK, done.  
https://svn.boost.org/trac/boost/ticket/9543

---

## Comment 4

> Username: k-satoda  
> Created_at: 2014-08-27 18:18:32 UTC  
> Url: https://github.com/boostorg/utility/pull/3#issuecomment-53617087  

Closed, as I confirmed that new [assert module](https://github.com/boostorg/assert) (which has been released in Boost 1.56.0) doesn't use iostreams any more. Thanks.

---
