# #36 - This seems to be a bug [Closed]

> Username: HppZ  
> Created at: 2019-01-04 09:05:18 UTC  
> Updated at: 2019-07-22 15:55:13 UTC  
> Closed at: 2019-04-10 13:39:07 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36  

parse xml file:  
  
c++ code:  
  
```c++  
std::locale utf8Locale(std::locale(), new std::codecvt_utf8<wchar_t>());  
std::wifstream f(*path);  
f.imbue(utf8Locale);  
wptree tree;  
read_xml(f, tree);  
```  
  
  
xml file content:  
  
```xml  
<?xml version="1.0" encoding="UTF-8"?>  
<data>  
<content>你好&#128549;</content>  
</data>  
```  
  
result is:  
 "你好ð¥"  
![image](https://user-images.githubusercontent.com/12285582/50680387-89f60b00-1042-11e9-91a5-b5fd27025caa.png)  
  
other info:  
boost property tree v 1.68.0  
VS 2017 15.9.4   
Windows 10 17763.195

---

## Comment 1

> Username: HppZ  
> Created at: 2019-01-04 09:50:56 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-451399315  

@kaalus @Beman @jewillco

---

## Comment 2

> Username: HppZ  
> Created at: 2019-01-08 00:20:00 UTC  
> Updated at: 2019-01-08 00:20:21 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-452129834  

@danieljames @Lastique @imikejackson Please HELP!

---

## Comment 3

> Username: Lastique  
> Created at: 2019-01-08 01:00:02 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-452137120  

It looks like rapidxml parser used in Boost.PropertyTree assumes that the output (parsed) encoding is UTF-8, regardless of the character type. Therefore the `&#128549;` constant gets decoded into 3 `wchar_t` elements instead of one or two depending on its encoding. The relevant code is here:  
  
https://github.com/boostorg/property_tree/blob/29a7f0390b6904e9b7c447d1e6e44442bd0ab17b/include/boost/property_tree/detail/rapidxml.hpp#L1506-L1532  
  
I believe, it should be specialized based on the resulting character type.  
  
@HppZ You may try parsing XML into a narrow-character `ptree` and then converting it from UTF-8 to your `wchar_t` encoding.  
  
PS: Mentioning everyone in the ticket won't get it solved sooner. It's actually quite rude, as now I have to spam people with my comment.

---

## Comment 4

> Username: HppZ  
> Created at: 2019-01-08 01:13:58 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-452139550  

I am really sorry and really thanks for your reply.  
  
so there is a bug in rapidxml parser but how to report this bug to RapidXml since I don't see any contact info on the website [http://rapidxml.sourceforge.net/](http://rapidxml.sourceforge.net/).

---

## Comment 5

> Username: Lastique  
> Created at: 2019-01-08 01:35:39 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-452143555  

There's an obfuscated email of the author on that page. But since we maintain a local fork in Boost.PropertyTree, we might as well fix it locally.

---

## Comment 6

> Username: HppZ  
> Created at: 2019-01-08 02:10:04 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-452149625  

that is great. looking forward to your coming fix.  
thanks!

---

## Comment 7

> Username: HppZ  
> Created at: 2019-07-22 08:47:18 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-513700741  

any plan to fix it? @Lastique

---

## Comment 8

> Username: Lastique  
> Created at: 2019-07-22 15:55:13 UTC  
> Url: https://github.com/boostorg/property_tree/issues/36#issuecomment-513847646  

I have no plans to proposing a fix currently.
