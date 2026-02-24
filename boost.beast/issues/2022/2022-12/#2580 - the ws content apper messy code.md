# #2580 - the ws content apper messy code [Closed]

> Username: cjdxhjj  
> Created at: 2022-12-12 08:25:04 UTC  
> Updated at: 2022-12-13 13:28:29 UTC  
> Closed at: 2022-12-13 13:28:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2580  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
If you rather keep your project secret, feel free  
to email the author at `<vinnie.falco@gmail.com>`; any  
private correspondence is treated as confidential.  
  
When reporting a bug please include the following:  
  
### Version of Beast  
1.80  
  
  
### Steps necessary to reproduce the problem  
  
i'm chinese, when my ws message contains chinese word, the server decode it as messy code  
![image](https://user-images.githubusercontent.com/5616997/206996229-f0cb59bd-3833-4129-a0b6-11049ee7e9f3.png)  
![image](https://user-images.githubusercontent.com/5616997/206996388-4344e032-3586-43ea-92cc-bd0318712aea.png)  
i'm using the buffers_to_string to decode the message  
![image](https://user-images.githubusercontent.com/5616997/206996534-12148bbf-8e55-42d7-a84d-18f583452412.png)  
  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: cjdxhjj  
> Created at: 2022-12-12 08:26:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346080896  

how can i special the charset to buffers_to_string or how to convert the charset at first

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-12-12 08:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346082720  

Did you check that the logger handles the charset fine? It's all UTF-8?

---

## Comment 3

> Username: cjdxhjj  
> Created at: 2022-12-12 08:29:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346083986  

yes, the logger log other chinese word correct

---

## Comment 4

> Username: cjdxhjj  
> Created at: 2022-12-12 08:29:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346084607  

![image](https://user-images.githubusercontent.com/5616997/206997630-d642ce73-0683-4b59-af9e-773cc2315a2f.png)

---

## Comment 5

> Username: cjdxhjj  
> Created at: 2022-12-12 08:30:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346085679  

![image](https://user-images.githubusercontent.com/5616997/206997828-9fe8191c-2002-46a8-939f-dba78419bb9d.png)

---

## Comment 6

> Username: cjdxhjj  
> Created at: 2022-12-12 08:36:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346091781  

my log is a wrapper of boost log  
  
![image](https://user-images.githubusercontent.com/5616997/206999011-b0a10cde-a781-421e-a508-f87a49fd516d.png)

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2022-12-12 09:17:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346143175  

What's the file encoding?

---

## Comment 8

> Username: cjdxhjj  
> Created at: 2022-12-12 09:32:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346162494  

log file encoding is ansi.

---

## Comment 9

> Username: cjdxhjj  
> Created at: 2022-12-12 09:34:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346164171  

i submit that data to printer, the paper display the chinese words as messy code

---

## Comment 10

> Username: cjdxhjj  
> Created at: 2022-12-12 15:22:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346691756  

when i tried convert the string from utf8 to ansi, the chinese words seems goods, but when i parse that string as json by boost json, the other issure apper, it can't parsed  
![image](https://user-images.githubusercontent.com/5616997/207081389-33650458-ad39-45c9-8ab2-1baf960f410a.png)  
i call the utf8_to_ansi  
![image](https://user-images.githubusercontent.com/5616997/207082027-3e638fc4-fd46-464c-951a-e82896329620.png)  
when i call the boost::json::parse, it throw an error  
![image](https://user-images.githubusercontent.com/5616997/207082236-22732bb0-e58e-4386-b61f-bebaa864b810.png)  
the same value parsed success by java  
![image](https://user-images.githubusercontent.com/5616997/207082374-2aa93779-34d1-4264-b120-48288a7e75b3.png)  
I have to convert the code a little bit  
![image](https://user-images.githubusercontent.com/5616997/207083423-5b43e78d-6997-4b77-b34d-3f81bf9bca27.png)

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-12-12 18:01:37 UTC  
> Updated at: 2022-12-12 18:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1346965573  

can you make a small self-contained program that reproduces the issue? Perhaps on https://godbolt.org ?

---

## Comment 12

> Username: cjdxhjj  
> Created at: 2022-12-13 00:58:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347593423  

@vinniefalco the link is https://godbolt.org/z/qqbaxss9G

---

## Comment 13

> Username: cjdxhjj  
> Created at: 2022-12-13 01:21:17 UTC  
> Updated at: 2022-12-13 01:23:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347612487  

my ws message payload is flowing:  
```  
{"action":6,"data":"[{\"type\":1,\"bold\":true,\"data\":\"11123244256222测试\"},{\"type\":1,\"bold\":true,\"data\":\"https://home.nxin.com\"}]"}  
```  
the data is a string and process by handler special by action  
  
when i convert the code at beast read handler, the data seems ok, but i can't parse it by boost json, i have to parse it at first, and convert it step by step.

---

## Comment 14

> Username: cjdxhjj  
> Created at: 2022-12-13 01:22:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347613488  

![image](https://user-images.githubusercontent.com/5616997/207203008-989f9575-8643-433c-a41f-691dbac1045c.png)

---

## Comment 15

> Username: vinniefalco  
> Created at: 2022-12-13 01:25:09 UTC  
> Updated at: 2022-12-13 01:29:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347614876  

JSON requires Unicode code points to be escaped. You can either convert to utf-8, or you can use the JSON Unicode escape. For example `"测试"` becomes `"\u6D4B\u8BD5"`.  
  
Also note that websocket messages of type TEXT (instead of BINARY) must have a valid utf-8 encoded string or else an error is raised.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2022-12-13 01:33:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347621070  

See: https://godbolt.org/z/zeh64PbPj

---

## Comment 17

> Username: cjdxhjj  
> Created at: 2022-12-13 01:36:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347623650  

@vinniefalco thanks very much, i will have a try convert at send time or binary format

---

## Comment 18

> Username: cjdxhjj  
> Created at: 2022-12-13 03:19:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2580#issuecomment-1347690457  

i'm using this code convert the chinese words to utf-8  
escape(str).replace(/\%u/g,'/u');
