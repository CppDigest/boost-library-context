# #36 - boost::signals2 deadlock with pthread [Closed]

> Username: AdrianRK  
> Created at: 2019-05-10 11:38:50 UTC  
> Updated at: 2019-05-15 22:57:37 UTC  
> Closed at: 2019-05-15 22:57:37 UTC  
> Url: https://github.com/boostorg/signals2/issues/36  

Hello,  
  
This issue seems to be back on version 1.65   
https://svn.boost.org/trac10/ticket/10721  
  
If the code is compiled with pthread then this will block on the first assert, if it is not build with pthread it will not block,  
g++ deadlock-boost-signals2.cpp -pthread -o output  
g++ deadlock-boost-signals2.cpp -o output2  
  
[deadlock-boost-signals2.zip](https://github.com/boostorg/signals2/files/3166276/deadlock-boost-signals2.zip)  
  
Thank you,  
Adrian Radu

---

## Comment 1

> Username: fmhess  
> Created at: 2019-05-10 16:45:06 UTC  
> Url: https://github.com/boostorg/signals2/issues/36#issuecomment-491354660  

It should be fixed on the develop branch now.  I also added your test to the signals2 test suite.

---

## Comment 2

> Username: fmhess  
> Created at: 2019-05-15 22:57:37 UTC  
> Url: https://github.com/boostorg/signals2/issues/36#issuecomment-492854427  

merged to master
