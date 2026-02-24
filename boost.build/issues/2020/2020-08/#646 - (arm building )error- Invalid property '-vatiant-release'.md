# #646 - (arm building )error: Invalid property '<vatiant>release': [Closed]

> Username: juliusplusplus  
> Created at: 2020-08-25 01:50:38 UTC  
> Updated at: 2020-08-25 06:17:48 UTC  
> Closed at: 2020-08-25 06:17:48 UTC  
> Url: https://github.com/boostorg/build/issues/646  

After excuting **./bootstrap.sh** ,I tried to execute **sudo ./bjam -a -d+2 cxxflags=-fPIC cflags=-fPIC linkflags=-fPIC --layout=system --build-type=minimal link=static threading=multi vatiant=release runtime-link=shared** Then I got the error:Invalid property '<vatiant>release': I cannot find the similar paper

---

## Comment 1

> Username: mclow  
> Created at: 2020-08-25 03:14:07 UTC  
> Url: https://github.com/boostorg/build/issues/646#issuecomment-679497534  

`vatiant=release` ??  Are you sure you typed that correctly?

---

## Comment 2

> Username: juliusplusplus  
> Created at: 2020-08-25 06:17:48 UTC  
> Url: https://github.com/boostorg/build/issues/646#issuecomment-679819255  

sorry,I misspelled it :)
