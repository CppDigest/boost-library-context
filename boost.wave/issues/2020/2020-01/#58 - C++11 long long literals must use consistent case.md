# #58 - C++11 long long literals must use consistent case [Closed]

> Username: jefftrull  
> Created at: 2020-01-13 22:12:48 UTC  
> Updated at: 2020-03-16 05:54:02 UTC  
> Closed at: 2020-03-16 05:54:02 UTC  
> Url: https://github.com/boostorg/wave/issues/58  

The following code is accepted by the Wave driver but rejected by Clang and gcc:  
```c++  
int main()  
{  
    auto foo = 111LL;     // OK  
    auto bar = 222ll;     // OK  
    auto baz = 333lL;     // should fail  
    auto quux = 444Ll;    // should also fail  
  
    return 0;  
}  
```  
(It also looks like the Github syntax highlighter notices the problem, which is interesting!)
