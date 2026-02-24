# #46 - Missing includes in 1.72 [Closed]

> Username: HowardHinnant  
> Created at: 2019-12-11 19:54:23 UTC  
> Updated at: 2019-12-20 05:09:21 UTC  
> Closed at: 2019-12-20 05:09:21 UTC  
> Url: https://github.com/boostorg/coroutine/issues/46  

On macOS:  
```  
In file included from /Users/howardhinnant/Development/boost_1_72_0/boost/coroutine/coroutine.hpp:10:  
/Users/howardhinnant/Development/boost_1_72_0/boost/coroutine/asymmetric_coroutine.hpp:2364:10: error: no member named 'begin' in namespace  
      'boost'; did you mean simply 'begin'?  
{ return boost::begin( c); }  
         ^~~~~~~  
```  
Ditto for `end`.

---

## Comment 1

> Username: olk  
> Created at: 2019-12-12 07:24:53 UTC  
> Url: https://github.com/boostorg/coroutine/issues/46#issuecomment-564884142  

asymmetric_coroutine.hpp:2364 points me to template declaration - I'm referring to branch develop:  
```  
template< typename Arg >  
struct range_mutable_iterator< coroutines::push_coroutine< Arg >, void >  
{ typedef typename coroutines::push_coroutine< Arg >::iterator type; };  
```  
Could you check branch develop please - unfortunately I've merged a pull request that was faulty.

---

## Comment 2

> Username: olk  
> Created at: 2019-12-12 09:11:26 UTC  
> Url: https://github.com/boostorg/coroutine/issues/46#issuecomment-564917198  

commit 436e1dbe6fcd31523d261d18ad011392f1d6fbbc should fix this issue but the commit was not added to release 1.72

---

## Comment 3

> Username: HowardHinnant  
> Created at: 2019-12-12 18:36:48 UTC  
> Updated at: 2019-12-12 18:39:18 UTC  
> Url: https://github.com/boostorg/coroutine/issues/46#issuecomment-565130329  

I did this:  
```  
git clone --recursive https://github.com/boostorg/boost.git  
cd boost  
git checkout develop  
./bootstrap.sh  
./b2 headers  
```  
And I don't think I picked up https://github.com/boostorg/coroutine/commit/436e1dbe6fcd31523d261d18ad011392f1d6fbbc  
```  
git show 436e1dbe6fcd31523d261d18ad011392f1d6fbbc  
fatal: bad object 436e1dbe6fcd31523d261d18ad011392f1d6fbbc  
```

---

## Comment 4

> Username: dmorilha-twilio  
> Created at: 2019-12-19 23:32:35 UTC  
> Url: https://github.com/boostorg/coroutine/issues/46#issuecomment-567723757  

bumped into the same issue, anything I can do about it?

---

## Comment 5

> Username: olk  
> Created at: 2019-12-20 05:09:21 UTC  
> Url: https://github.com/boostorg/coroutine/issues/46#issuecomment-567789931  

patch is available at boost.org
