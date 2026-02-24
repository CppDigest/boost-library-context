# #295 - Can I adjust the accuracy of timer? [Open]

> Username: zhmt  
> Created at: 2022-04-07 06:50:46 UTC  
> Updated at: 2022-04-07 06:55:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/295  

Failed to achieve  FPS 60 ( frame per second) :  
```  
auto timenow() {  
  return std::chrono::duration_cast<std::chrono::milliseconds>(std::chrono::system_clock::now().time_since_epoch()).count();  
}  
  
int main() {  
  boost::fibers::fiber mainFiber([]()   
    {  
      while (true) {  
        auto now = timenow();  
        boost::this_fiber::sleep_for(std::chrono::milliseconds(16));  
        std::cout << timenow() - now << "\n";  
      }  
    });  
  mainFiber.join();  
}  
```  
  
Outputs ( should be 16 ):  
  
```  
34  
27  
30  
31  
31  
31  
30  
31  
30  
29  
```
