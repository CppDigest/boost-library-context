# #265 - Parser blocks trying to read non-existing body when parsing headers separately [Closed]

> Username: mika-fischer  
> Created at: 2017-02-13 10:43:51 UTC  
> Updated at: 2017-05-05 02:16:37 UTC  
> Closed at: 2017-05-05 02:16:37 UTC  
> Url: https://github.com/boostorg/beast/issues/265  

This is a followup to #257. The fix for that bug unfortunately introduced another bug, which can be observed using the same examples: [coroutine version](https://gist.github.com/mika-fischer/cb93b1071d9eea492f27234f7fb6d1b2) [callback version](https://gist.github.com/mika-fischer/a54a68769985afe0f22967881513cbb8).  
  
The problem is that for requests without a body, the parser will block and wait for data. This can be tested with a simple `curl http://localhost:8081` which hangs indefinitely. When reading a complete `http::request<http::string_body>`, everything works fine.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-13 14:00:56 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-279399824  

I can reproduce it, thanks for the example. At this point its starting to look like I have to add one hack on top of another...the design of split parsing was not well thought out. We might just have to say that this feature does not function correctly. I am in the middle of building a new parser. I will address it there. Unless you have a suggestion on how we might fix this robustly in the current version.

---

## Comment 2

> Username: mika-fischer  
> Created at: 2017-02-13 14:31:23 UTC  
> Updated at: 2017-02-13 14:31:59 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-279407740  

I see. I haven't had the time to look deeper into the parser, and since it's soon to be replaced, I don't think it makes sense to start now.  
We'll just wait for the new parser, and try to get by with reading whole requests in the meantime.  
  
If you need comments or testing, let me know!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-03-29 13:48:30 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-290095335  

@mika-fischer I could use your help now!!

---

## Comment 4

> Username: mika-fischer  
> Created at: 2017-03-29 14:29:14 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-290107742  

This immediate issue seems to be fixed with the http branch. Concerning the general design, I'll comment in #154.

---

## Comment 5

> Username: mika-fischer  
> Created at: 2017-03-30 07:58:23 UTC  
> Updated at: 2017-03-30 08:50:41 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-290332511  

Actually, I have to take that back, it seems this, or rather #257 is back with the http branch...  
  
All body sizes larger than 875 bytes when used with curl cause beast to hang waiting for more data. This happens with split header and body parsing, but also when reading the whole request at once.  
  
New code: https://gist.github.com/mika-fischer/f01cb90e5b6d3ee0fa506c0c2a0fa28a  
  
Test with:  
```  
dd if=/dev/zero of=test.bin bs=1 count=875  
curl --data-binary @test.bin -H 'Expect:' http://127.0.0.1:8090  
dd if=/dev/zero of=test.bin bs=1 count=876  
curl --data-binary @test.bin -H 'Expect:' http://127.0.0.1:8090  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-03-30 11:48:20 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-290387293  

>Actually, I have to take that back, it seems this, or rather #257 is back with the http branch...  
  
That's amazing, I managed to reproduce the same defect in completely rewritten code! Here's the fix:  
https://github.com/vinniefalco/Beast/commit/a2a9893d98ed51755c64cc55eca85b774dc78312  
  
Thanks for providing a reproducible test case, that made it very easy to track down!

---

## Comment 7

> Username: mika-fischer  
> Created at: 2017-03-30 13:31:41 UTC  
> Url: https://github.com/boostorg/beast/issues/265#issuecomment-290411262  

Thanks, I'll test it soon. Maybe it would make sense to add this as a test, i.e. send requests with increasing body sizes and see whether they are parsed correctly?
