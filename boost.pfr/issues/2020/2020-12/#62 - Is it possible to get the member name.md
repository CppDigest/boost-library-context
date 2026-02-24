# #62 - Is it possible to get the member name [Closed]

> Username: inie0722  
> Created at: 2020-12-08 01:18:05 UTC  
> Updated at: 2020-12-19 01:43:47 UTC  
> Closed at: 2020-12-19 01:43:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/62  

example  
```c++  
struct my_struct{  
  std::string name;  
  unsigned boost;  
  int pfr;  
};  
  
my_struct val;  
cout << get_name<0>(val) << " " << get_name<1>(val) <<   " " << get_name<2>(val)  << endl;  
// print "name boost pfr"  
```  
  
This is very useful for json serialization

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-15 14:32:59 UTC  
> Url: https://github.com/boostorg/pfr/issues/62#issuecomment-745331827  

OMG  
  
There is a way https://godbolt.org/z/jx97n9 !  
  
I'll try to add that functionality soon

---

## Comment 2

> Username: inie0722  
> Created at: 2020-12-19 01:43:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/62#issuecomment-748400033  

Thank you I am looking forward to the launch of this feature
