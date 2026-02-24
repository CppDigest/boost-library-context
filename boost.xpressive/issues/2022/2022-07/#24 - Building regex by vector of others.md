# #24 - Building regex by vector of others [Open]

> Username: asergunov  
> Created at: 2022-07-08 14:38:07 UTC  
> Updated at: 2022-07-08 14:52:16 UTC  
> Url: https://github.com/boostorg/xpressive/issues/24  

Hello.  
  
I need to find which regex of N my string matches.  
  
It's easy to implement if I have them compile time:  
```cpp  
sregex re = (s1 = re1)|(s2 = re2)|(s3=re3);  
...  
std::set<int> foo(const std::string& str) {  
 std::set<int> result;  
 smatch match;  
 if(regex_match(str, match, re)) {  
 for(int i = 0; i<3; ++i)  
  if(match[i+1]) result.insert(i);  
 }  
return result;  
}  
```  
  
But I have a vector:  
```cpp  
std::set<int> foo(const std::string& str, const std::vector<sregex>& reVector) {  
 sregex re;  
 int index = 1;  
 for(auto&& item : reVector) {  
   re |= (mark_tag(index++) = item); // (1)  
 }  
  
 std::set<int> result;  
 smatch match;  
 if(regex_match(str, match, re)) {  
  for(int i = 0; i<reVector; ++i)  
    if(match[i+1]) result.insert(i);  
  }  
 }  
return result;  
}  
```  
  
There at (1) `|=` doesn't work as I expect for some reson, It didn't catch some values.  
If I replace it with   
```cpp  
 re = re | (mark_tag(index++) = item);  
```  
it just crashes. If I replace with:  
```cpp  
 auto t = re;  
 re = t | (mark_tag(index++) = item);  
```  
it making captures of previous tags to be subexpresions matches. So code doesn't work as I want.  
  
Is it any way to prevent sub-expression capture isolation?  
  
As I understand it should be some intermediate type before sregex to express expression under construction. So it will not isolate matches of existing part.  
  
I'm using v1.65.1
