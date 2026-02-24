# #479 - JSON compliance [Closed]

> Username: jano42  
> Created at: 2020-12-18 10:05:30 UTC  
> Updated at: 2021-01-06 10:34:55 UTC  
> Closed at: 2021-01-06 10:34:55 UTC  
> Url: https://github.com/boostorg/json/issues/479  

Hi  
  
Is youre library is fully JSON compliant ?  
  
I ask because in our opensources software [(yadoms)](https://github.com/Yadoms/yadoms) (with also @sgallou), I was using JSON data through boost::property_tree (which have methods for exporting and importing Json). But the property_tree was not compliant with JSON (managing arrays is a "hack" and not compliant, particularly with "empty arrays").  
  
I'm currently working on replacing boost::property_tree with full json compliant lib. I've started to use rapidJson, and recently I've  found boost::json !! SO I'd like to know if the library is fully compliant.  
  
In fact I would prefer to use your library, because it will reduces dependencies (we already use boost), and rapidJson is not really updated (no release since 2016, but lots of commits are done), and I found its allocator not very good (for my cases).  
  
Your lib is real good opportunity for me to use a more robust lib (I expect for that, but I know the boost include only libraries that are really tested and works on many platforms).  
  
  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-12-18 15:16:24 UTC  
> Url: https://github.com/boostorg/json/issues/479#issuecomment-748147709  

![image](https://user-images.githubusercontent.com/1503976/102630068-ee5fe700-4100-11eb-97ef-ff90a526f1d2.png)
