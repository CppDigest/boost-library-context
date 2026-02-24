# #374 - synchronized_value documentation example fails to compile [Open]

> Username: Jacob-Burckhardt  
> Created at: 2022-07-15 20:42:21 UTC  
> Updated at: 2022-07-15 20:42:21 UTC  
> Url: https://github.com/boostorg/thread/issues/374  

The [documentation](https://github.com/boostorg/thread/blob/develop/doc/synchronized_value.qbk) gives the following example.  u1 and u2 appear to be variable names, yet they appear to be undeclared.  
  
```  
  synchronized_value<std::queue<MessageType> > q1,q2;  
  void transferMessage()  
  {  
    auto lks = synchronize(u1,u2); // dead-lock free algorithm  
  
    if(!std::get<1>(lks)->empty())  
    {  
      std::get<2>(lks)->push_back(u1->front());  
      std::get<1>(lks)->pop_front();  
    }  
  }  
    
```
