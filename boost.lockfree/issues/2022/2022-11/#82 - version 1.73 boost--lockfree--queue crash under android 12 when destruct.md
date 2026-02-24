# #82 - version 1.73 boost::lockfree::queue crash under android 12 when destruct? [Open]

> Username: rooong  
> Created at: 2022-11-25 07:47:05 UTC  
> Updated at: 2022-11-25 07:52:32 UTC  
> Url: https://github.com/boostorg/lockfree/issues/82  

version 1.73 boost::lockfree::queue crash under android 12 when destruct?  
eg.  
{  
  boost::lockfree::queue<int, boost::fixed_sized<false> > test_que(10);  
}  
it does nothing else, it crash backtrace like this:  
**boost::lockfree::queue<int, boost::fixed_sized<false> >::~queue()+272**  
  
the same apk in not android 12 phone, it works ok, but android 12  
  
what happened?
