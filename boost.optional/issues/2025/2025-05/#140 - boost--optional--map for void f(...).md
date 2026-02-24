# #140 - [Idea] boost::optional::map for void f(...) [Open]

> Username: mknaleczb  
> Created at: 2025-05-25 16:09:46 UTC  
> Updated at: 2025-05-25 16:09:46 UTC  
> Url: https://github.com/boostorg/optional/issues/140  

As I work boost::optional I have a usecase, where I retrieve optional value and I do not necessary want to transform it, but just to use it if it is there, and if not to ignore it.   
Let's assume scenario:  
```  
boost::optional<std::string> GetTextOpt();  
int stoi(std::string_view);  
void SleepMillis(int);  
```  
  
In current implementation, workarounds are necessary, like:  
```  
// v1  
if(auto res = GetTextOpt().map(stoi)) SleepMillis(res);  
// v2  
GetTextOpt().map(stoi).map([](int i){SleepMillis(i); return int{};})  
```  
  
Proposal:  
Provide new method (e.g. boost::optional::consume) as implementation:  
  
```  
template <typename F>  
void consume(F f) [&,const&,&&]  
  {  
    if (this->has_value())  
      f(get());  
  }  
```
