# #6 http::prepare and more [Merged]

> Username: vinniefalco  
> Created at: 2016-05-02 23:20:28 UTC  
> Updated at: 2016-05-06 21:03:16 UTC  
> Merged at: 2016-05-06 19:22:52 UTC  
> Closed at: 2016-05-06 19:22:52 UTC  
> Url: https://github.com/boostorg/beast/pull/6  

A lot of refactoring, tests, docs, and fine tuning of the interfaces.  
  
Documentation preview: http://vinniefalco.github.io/stage/beast/

---

## Comment 1

> Username: ximinez  
> Created_at: 2016-05-03 21:47:02 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61962027  

Would `buf_len_ = static_cast<std::size_t>(std::min(size_ - offset_, sizeof(buf_)));` be more efficient, or does upcasting `sizeof(buf_)` only to downcast it back make it not worth it?

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2016-05-03 21:53:02 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61962825  

The answer is revealed when you try to make the code work for both 32-bit and 64-bit architectures :-) Note that the file size is stored as `std::uint64_t`. The websocket code handles this a bit more elegantly with the `clamp` functions:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/websocket/detail/stream_base.hpp#L42

---

## Comment 3

> Username: ximinez  
> Created_at: 2016-05-03 22:11:46 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61965185  

Formatting aside, this is functionally equivalent to  
  
```  
       if(! msg.headers.exists("Content-Length") &&  
            ! rfc2616::token_in_list(  
                msg.headers["Transfer-Encoding"], "chunked") &&  
                    ! rfc2616::token_in_list(  
                        msg.headers["Connection"], "close"))  
            msg.headers.insert("Connection", "close");  
```  
  
Yes?

---

## Comment 4

> Username: HowardHinnant  
> Created_at: 2016-05-03 22:41:38 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61968864  

From `~writer()` it looks like you intend for `writer` to be non-copyable.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-05-03 22:52:12 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61969954  

I think so.. Both versions are hard to read.. lol

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-05-03 22:52:49 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61970027  

I hadn't given it any thought. The implementation never makes copies or tries to move a writer, its an ephemeral object.

---

## Comment 7

> Username: HowardHinnant  
> Created_at: 2016-05-03 22:59:02 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61970663  

I got this example to run.  I have no idea what it is trying to do, but it sure spent quite a while trying to do it!

---

## Comment 8

> Username: HowardHinnant  
> Created_at: 2016-05-03 23:01:36 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61970928  

If your customers copy this example, and then copy `writer` in their code.  You either want it to work, or not compile.

---

## Comment 9

> Username: HowardHinnant  
> Created_at: 2016-05-03 23:03:40 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61971145  

Nice clear simple example.  Well commented!

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2016-05-03 23:46:38 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61974812  

It fetches the home page of the 10,000 top visited domains ranked by Alexa. A lot of them time out, which causes the program to run for a long time. Future improvements could make it asynchronous and try to visit multiple sites concurrently.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2016-05-03 23:47:40 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r61974880  

Heh thanks! The example program was your idea

---

## Comment 12

> Username: ximinez  
> Created_at: 2016-05-04 15:38:38 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62062253  

Aha. Of course, I've gotten so used to 64-bit arch, that I often forget to consider 32-bit. I need to stop doing that.

---

## Comment 13

> Username: HowardHinnant  
> Created_at: 2016-05-04 16:36:16 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62071776  

This is very optional.  Actually in the obsessive polishing category...    
  
You could eliminate this overload: `write_streambuf(Streambuf&)` by changing the above code from:  
  
```  
    write_streambuf(streambuf, t0);  
    write_streambuf(streambuf, t1);  
    write_streambuf(streambuf, tn...);  
```  
  
to  
  
```  
    write_streambuf(streambuf, t0);  
    write_streambuf(streambuf, t1, tn...);  
```

---

## Comment 14

> Username: ximinez  
> Created_at: 2016-05-04 18:33:25 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62090863  

Is this assuming a particular native data layout? Is that cross-platform safe?

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2016-05-04 19:01:13 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62095542  

it _should_ work on all platforms - please verify!

---

## Comment 16

> Username: HowardHinnant  
> Created_at: 2016-05-04 19:19:18 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62098137  

This looks right (and portable) to me.

---

## Comment 17

> Username: ximinez  
> Created_at: 2016-05-04 19:38:14 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62100885  

Sounds good. I wasn't sure, so I figured it would be better to double-check.

---

## Comment 18

> Username: HowardHinnant  
> Created_at: 2016-05-04 21:19:30 UTC  
> Url: https://github.com/boostorg/beast/pull/6#issuecomment-217006099  

👍 Looks good to me.

---

## Comment 19

> Username: ximinez  
> Created_at: 2016-05-05 18:36:58 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62233098  

You use `Traits::copy(&s_[0], &s[0], M);` everywhere in the file except here. Is there a reason, or should this one be changed for consistency.

---

## Comment 20

> Username: ximinez  
> Created_at: 2016-05-05 18:47:44 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62234793  

Silly question: why not implement this as `return -compare(rhs, s);` to call the other override, and reduce code duplication (particularly if you declare this override as `inline`)?

---

## Comment 21

> Username: ximinez  
> Created_at: 2016-05-05 19:03:41 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62237222  

Oooh. Close call there.

---

## Comment 22

> Username: ximinez  
> Created_at: 2016-05-05 19:09:16 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62237975  

Are the `static_assert`s redundant in the overloads that throw, since they call overloads that don't throw, which have duplicate checks? I suppose it doesn't hurt since they are static after all, but I figured it's worth asking.

---

## Comment 23

> Username: ximinez  
> Created_at: 2016-05-05 19:41:07 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62242653  

Test that values were copied, and not pointers:  
`expect(s3 == "x");`

---

## Comment 24

> Username: ximinez  
> Created_at: 2016-05-05 19:56:36 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62244641  

```  
    {  
        str1 s1("x");  
        str2 s2("y");  
        expect(! (s1 == s2));  
        expect(s1 <= s2);  
        expect(! (s1 >= s2));  
        expect(s1 < s2);  
        expect(! (s1 > s2));  
        expect(s1 != s2);  
    }  
```

---

## Comment 25

> Username: ximinez  
> Created_at: 2016-05-05 19:58:15 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62244879  

Good equality tests there, but need some inequality / ordering tests, too.

---

## Comment 26

> Username: ximinez  
> Created_at: 2016-05-05 20:00:52 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62245269  

Same here:  
  
```  
         {  
            str2 s("x");  
            expect(! (s == "y"));  
            expect(s <= "y");  
            expect(! (s >= "y"));  
            expect(s < "x");  
            expect(! (s > "x"));  
            expect(s != "x");  
            expect(! ("y" == s));  
            expect(! ("y" <= s));  
            expect("y" >= s);  
            expect(! ("y" < s));  
            expect("y" > s);  
            expect("y" != s);  
        }  
```

---

## Comment 27

> Username: ximinez  
> Created_at: 2016-05-05 20:03:55 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62245688  

Move it back here, too? `srs = std::move(srs2);`

---

## Comment 28

> Username: ximinez  
> Created_at: 2016-05-05 21:36:44 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62259592  

_Really_ commented out!

---

## Comment 29

> Username: vinniefalco  
> Created_at: 2016-05-05 22:11:47 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62263941  

That's a bug. It should use `Traits::copy`

---

## Comment 30

> Username: vinniefalco  
> Created_at: 2016-05-05 22:12:25 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62264016  

That's a good idea ^_^  
  
If you're really asking me _why_ it was done this way, probably because I was trying to write it as quickly as possible.

---

## Comment 31

> Username: vinniefalco  
> Created_at: 2016-05-05 22:13:18 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62264118  

Think about what you're saying :-) study the declaration.

---

## Comment 32

> Username: vinniefalco  
> Created_at: 2016-05-05 22:14:04 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62264194  

Yeah whoops. Found that by accident

---

## Comment 33

> Username: vinniefalco  
> Created_at: 2016-05-05 22:14:52 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62264284  

Great question. They are redundant. The thinking is that if they go off, the user will get an error message that is very close in the source file to the signature of the function they are trying to call, instead of a function with a different signature.

---

## Comment 34

> Username: vinniefalco  
> Created_at: 2016-05-06 12:43:42 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62324632  

This will be addressed in a subsequent PR that also increases coverage

---

## Comment 35

> Username: vinniefalco  
> Created_at: 2016-05-06 12:45:17 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62324777  

Will fix, thanks for the code paste

---

## Comment 36

> Username: vinniefalco  
> Created_at: 2016-05-06 12:46:34 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62324893  

This fails:  
  
```  
expect(s < "x");  
```

---

## Comment 37

> Username: ximinez  
> Created_at: 2016-05-06 18:29:14 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62370934  

Bad things will happen here if HTTP ever gets to a 3 digit (`1.25`) or subdivided (`1.1.5`) version number.

---

## Comment 38

> Username: vinniefalco  
> Created_at: 2016-05-06 18:39:38 UTC  
> Updated_at: 2016-05-06 18:51:36 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62372397  

As HTTP/2 is already out, I have confidence that we are okay. Anyway, I"m going to refactor this code because its not getting coverage (and likely never will, because we will probably never see anything new below HTTP/2 in our lifetime).

---

## Comment 39

> Username: ximinez  
> Created_at: 2016-05-06 19:25:26 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62378672  

IMHO, this works better as one sentence. "These use-cases are important, but this library does not try to do that."

---

## Comment 40

> Username: ximinez  
> Created_at: 2016-05-06 20:05:52 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62383733  

"the error _thrown_ from this function".

---

## Comment 41

> Username: ximinez  
> Created_at: 2016-05-06 20:24:21 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62386012  

Moving this comment to the correct line: "the error return_ed_ from this function"

---

## Comment 42

> Username: vinniefalco  
> Created_at: 2016-05-06 20:34:21 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62387283  

Thanks for the edits. They will go in the next PR

---

## Comment 43

> Username: ximinez  
> Created_at: 2016-05-06 20:38:04 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62387739  

Typo: operation

---

## Comment 44

> Username: ximinez  
> Created_at: 2016-05-06 20:51:35 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62389321  

This file can be deleted. It is a duplicate of `test/http/body_type.cpp`, but does not appear to be referenced anywhere.

---

## Comment 45

> Username: ximinez  
> Created_at: 2016-05-06 20:55:38 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62389840  

Good thinking.

---

## Comment 46

> Username: vinniefalco  
> Created_at: 2016-05-06 20:57:21 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62390061  

Yep, will be removed in b4.

---

## Comment 47

> Username: ximinez  
> Created_at: 2016-05-06 20:57:26 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62390068  

Copy/paste error. All the comparisons should be against `"y"`.  
  
```  
         {  
            str2 s("x");  
            expect(! (s == "y"));  
            expect(s <= "y");  
            expect(! (s >= "y"));  
            expect(s < "y");  
            expect(! (s > "y"));  
            expect(s != "y");  
            expect(! ("y" == s));  
            expect(! ("y" <= s));  
            expect("y" >= s);  
            expect(! ("y" < s));  
            expect("y" > s);  
            expect("y" != s);  
        }  
```

---

## Comment 48

> Username: ximinez  
> Created_at: 2016-05-06 20:59:13 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62390253  

Ah. I was pattern matching with the previous block, and missed that this declared with a reference.

---

## Comment 49

> Username: ximinez  
> Created_at: 2016-05-06 21:03:16 UTC  
> Url: https://github.com/boostorg/beast/pull/6#discussion_r62390675  

That's fair.

---
