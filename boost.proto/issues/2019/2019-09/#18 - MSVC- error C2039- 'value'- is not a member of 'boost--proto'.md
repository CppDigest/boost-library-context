# #18 - MSVC: error C2039: 'value': is not a member of 'boost::proto' [Closed]

> Username: matbech  
> Created at: 2019-09-23 19:57:20 UTC  
> Updated at: 2020-03-27 12:53:22 UTC  
> Closed at: 2019-09-23 20:09:22 UTC  
> Url: https://github.com/boostorg/proto/issues/18  

MSVC 16.4.0 Preview 1 fails with this compile error:  
boost\boost\proto\generate.hpp(239,20): error C2039: 'value': is not a member of 'boost::proto'  
  
Affected line:  
            proto::value(that.proto_expr_) = proto::value(e);

---

## Comment 1

> Username: matbech  
> Created at: 2019-09-23 20:09:22 UTC  
> Url: https://github.com/boostorg/proto/issues/18#issuecomment-534263348  

Ah, it seems this was already fixed with this commit in the develop branch:  
https://github.com/boostorg/proto/pull/2/files

---

## Comment 2

> Username: k15tfu  
> Created at: 2019-10-09 17:03:13 UTC  
> Url: https://github.com/boostorg/proto/issues/18#issuecomment-540093709  

Hi @ericniebler ! I have the same issue in VC2019. Do you know why it wasn't included in 1.71?

---

## Comment 3

> Username: zhongwm  
> Created at: 2019-12-05 03:47:12 UTC  
> Url: https://github.com/boostorg/proto/issues/18#issuecomment-561955449  

I have the same issue in VC2019, yesterday updated VS2019 community to newest, compilation broken.

---

## Comment 4

> Username: UnknownObject000  
> Created at: 2020-03-27 12:53:22 UTC  
> Url: https://github.com/boostorg/proto/issues/18#issuecomment-604984199  

I have the same issue in VC1029 too,mine is Visual Studio 2019
