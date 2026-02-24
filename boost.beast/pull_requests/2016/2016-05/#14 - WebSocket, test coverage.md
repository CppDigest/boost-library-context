# #14 WebSocket, test coverage [Merged]

> Username: vinniefalco  
> Created at: 2016-05-18 19:44:03 UTC  
> Updated at: 2016-05-25 20:20:38 UTC  
> Merged at: 2016-05-25 20:20:34 UTC  
> Closed at: 2016-05-25 20:20:34 UTC  
> Url: https://github.com/boostorg/beast/pull/14  

Tons of fixes including several crashes. Major coverage increase.

---

## Comment 1

> Username: codecov-io  
> Created_at: 2016-05-18 20:15:38 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-220144602  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/14?src=pr) is **96.97%**  
  
> Merging [#14](https://codecov.io/gh/vinniefalco/Beast/pull/14?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will increase coverage by **4.53%**  
  
``` diff  
@@             master        #14   diff @@  
==========================================  
  Files            69         70     +1     
  Lines          4630       4819   +189     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
+ Hits           4280       4673   +393     
+ Misses          350        146   -204     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [c0952e5...2c1dbd5](https://codecov.io/gh/vinniefalco/Beast/compare/c0952e54db7bd519440dc0611db7347cb048296d...2c1dbd519006434de68b9f2dad8071753e091d72)

---

## Comment 2

> Username: seelabs  
> Created_at: 2016-05-20 14:48:17 UTC  
> Updated_at: 2016-05-20 15:39:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-220626521  

gcc 6.1 issues the following warning:   
`/home/swd/projs/common/boost_1_61_0/boost/function/function_template.hpp:572:11: warning: placement new constructing an object of type ‘boost::_bi::bind_t<std::vector<boost::program_options::basic_option<char> >, boost::_mfi::mf1<std::vector<boost::program_options::basic_option<char> >, boost::program_options::detail::cmdline, std::vector<std::__cxx11::basic_string<char> >&>, boost::_bi::list2<boost::_bi::value<boost::program_options::detail::cmdline*>, boost::arg<1> > >’ and size ‘24’ in a region of type ‘char’ and size ‘1’ [-Wplacement-new=]  
           new (reinterpret_cast<void*>(&functor.data)) FunctionObj(f);  
           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
Both clang 3.8 and gcc 5.1 compile clean. This looks more like a boost issue than a beast issue. Just a heads up and something to look at later.  
I

---

## Comment 3

> Username: seelabs  
> Created_at: 2016-05-20 14:51:50 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64053809  

`full_name` does string concatenation, which may alloc. I don't like that in a compare. We could use `std::tuple` to do the comparison without allocating:  
  
```  
    friend  
    bool  
    operator<(suite_info const& lhs, suite_info const& rhs)  
    {  
        return std::tie (lhs.library_, lhs.module_, lhs.name_) <  
            std::tie (rhs.library_, rhs.module_, rhs.name_);  
    }  
```  
  
Edit: Changed order of tuple to swap `library_` and `name_`

---

## Comment 4

> Username: nbougalis  
> Created_at: 2016-05-20 15:03:58 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64055892  

This would likely error in `UNICODE` builds. Why not this instead:  
  
```  
str({});  
```  
  
If that (for some reason) doesn't work, the more explicit alternative will:  
  
```  
str(std::basic_string<CharT, Traits, Allocator>{});  
```  
  
The same applies to the Windows version.

---

## Comment 5

> Username: nbougalis  
> Created_at: 2016-05-20 15:17:04 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64057920  

This does do the right thing - `member` is from `boost::base_from_member` which has already been initialized at this point.

---

## Comment 6

> Username: nbougalis  
> Created_at: 2016-05-20 15:22:27 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64058918  

Why is `this->` needed here and a couple of lines below?

---

## Comment 7

> Username: seelabs  
> Created_at: 2016-05-20 15:27:02 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64059795  

I'm not sure what this is doing. Preventing automatic conversions to `char const*` maybe?

---

## Comment 8

> Username: seelabs  
> Created_at: 2016-05-20 15:28:15 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64060020  

insensitve -> insensitive

---

## Comment 9

> Username: nbougalis  
> Created_at: 2016-05-20 15:28:47 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64060109  

Why? Should this just be removed?

---

## Comment 10

> Username: nbougalis  
> Created_at: 2016-05-20 15:29:18 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64060188  

Why? Should this just be removed?

---

## Comment 11

> Username: seelabs  
> Created_at: 2016-05-20 15:33:14 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64060901  

@nbougalis I had the same question. gcc give a nice explanation (along with a fix!):  
  
`extras/beast/unit_test/suite.hpp:76:16: error: ‘str’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
             str("");  
                ^  
extras/beast/unit_test/suite.hpp:76:16: note: declarations in dependent base ‘std::__cxx11::basic_stringbuf<char>’ are not found by unqualified lookup  
extras/beast/unit_test/suite.hpp:76:16: note: use ‘this->str’ instead`

---

## Comment 12

> Username: nbougalis  
> Created_at: 2016-05-20 15:44:08 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64062690  

"consumed in the input buffer" should be "consumed from the input buffer"

---

## Comment 13

> Username: nbougalis  
> Created_at: 2016-05-20 15:52:42 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64064008  

The commit message needs a bit of a cleanup. The first paragraph of the second sentence says that this fixes a bug, repeating what the header says:  
  
> Fix parser off by one accessing buffer bug:  
> This fixed a bug where in some cases the parser could dereference past the end of the caller provided buffer.  
  
Consider instead:  
  
> Fix off-by-one buffer accessing bug in parser:  
> In some case the parser would dereference past the end of the caller provided buffer.

---

## Comment 14

> Username: nbougalis  
> Created_at: 2016-05-20 16:02:19 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64065450  

Nice!

---

## Comment 15

> Username: nbougalis  
> Created_at: 2016-05-20 18:03:17 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64082562  

What?

---

## Comment 16

> Username: seelabs  
> Created_at: 2016-05-20 19:28:49 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64094987  

A couple spelling errors in this file (sycnhronously here). Cherry pick this to fix what I found: https://github.com/seelabs/Beast/commit/db564b94bc06410548a833c3f16a0c9dfe5e712d

---

## Comment 17

> Username: seelabs  
> Created_at: 2016-05-20 20:31:32 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64102935  

How is `testClose` called? Is this dead code?

---

## Comment 18

> Username: seelabs  
> Created_at: 2016-05-20 20:44:39 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64104763  

Nit: We don't need `R` `class =` should work.

---

## Comment 19

> Username: seelabs  
> Created_at: 2016-05-20 20:51:08 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64105519  

Nit: `explicit` not needed.

---

## Comment 20

> Username: vinniefalco  
> Created_at: 2016-05-20 23:24:23 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64120251  

test of self-assignment

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2016-05-20 23:26:08 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64120370  

It should be put back, I commented it out to see if it fixed a crashing bug and forgot to put it back

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2016-05-20 23:27:50 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64120479  

Yes

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2016-05-22 13:52:00 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64150184  

Its currently not called but I plan on circling back at some point and trying to get those last few lines that don't have coverage.

---

## Comment 24

> Username: vinniefalco  
> Created_at: 2016-05-22 13:53:12 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64150200  

FYI, I use UK spelling sometimes (e.g. initialise)

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2016-05-22 13:55:22 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-220833763  

Incorporated all review feedback and rewrote the branch

---

## Comment 26

> Username: seelabs  
> Created_at: 2016-05-22 21:42:24 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64156819  

I like how the state machine turned out with the enum and the `case state+1:` idiom. One idea is to reserve some low-order bits for this purpose. i.e all enums are numbered sequentially with the pattern `do_start = 0 << 2, do_read_payload = 1 << 2,` etc. That makes it easy to audit code (if I ever see `state+4` I know it's an error).

---

## Comment 27

> Username: vinniefalco  
> Created_at: 2016-05-23 12:13:50 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-220963457  

I added a commit to update the documentation. The HTML preview is here:  
http://vinniefalco.github.io/stage/beast/index.html

---

## Comment 28

> Username: seelabs  
> Created_at: 2016-05-23 12:52:58 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-220971591  

:+1: Code looks good & tests look good.I see the travis coverage build is failing with the "negative counts" issue, I wish I knew what was going on with that.

---

## Comment 29

> Username: vinniefalco  
> Created_at: 2016-05-23 12:54:31 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-220971956  

"negative counts" is a spurious failure that is often solved merely by restarting the build.

---

## Comment 30

> Username: nbougalis  
> Created_at: 2016-05-24 01:33:58 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64316572  

I'd inline the body of `print(std::ostream&, suite_list const&)` in here.

---

## Comment 31

> Username: nbougalis  
> Created_at: 2016-05-24 02:18:14 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64319321  

You use "object" and "socket" to refer to, seemingly, the same thing. This can be a bit confusing. Might make more sense if you did this:  
  
> For servers that can handshake in multiple protocols, it may be desired to wrap an object that already exists, such as a socket. Such objects can be moved in:

---

## Comment 32

> Username: nbougalis  
> Created_at: 2016-05-24 02:21:03 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64319495  

Consider using the canonical `example.com` domain in your examples and documentation - see [RFC 2606](https://tools.ietf.org/html/rfc2606).

---

## Comment 33

> Username: nbougalis  
> Created_at: 2016-05-24 02:22:42 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64319699  

This is a super-minor nit, and I get that reasonable people can view this topic differently - just pointing it out: "an HTTP response" reads better than "a HTTP response".

---

## Comment 34

> Username: vinniefalco  
> Created_at: 2016-05-24 02:27:50 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64320022  

The problem is that `boost::asio::ssl::stream` is not a socket, but yet it is suitable as a `NextLayer` type in `beast::websocket::stream`.

---

## Comment 35

> Username: vinniefalco  
> Created_at: 2016-05-24 02:28:17 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64320044  

Good idea

---

## Comment 36

> Username: vinniefalco  
> Created_at: 2016-05-24 02:29:07 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64320092  

What reads better:  
"an hypertext transfer protocol response"  
                   -or-  
"a hypertext transfer protocol response"  
                    ?  
  
Both are correct, as long as usage is consistent.

---

## Comment 37

> Username: nbougalis  
> Created_at: 2016-05-25 11:52:25 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64558573  

I suspect that the "correct" usage is:  
  
> a hypertext transfer protocol response   
  
and   
  
> an HTTP response  
  
According to http://blog.apastyle.org/apastyle/2012/04/using-a-or-an-with-acronyms-and-abbreviations.html "trick here is to use your ears (how the acronym is pronounced), not your eyes (how it's spelled)." which isn't perfect, but it tends to work.

---

## Comment 38

> Username: nbougalis  
> Created_at: 2016-05-25 12:13:25 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64560933  

If you're going to test self-assignment, then you should ensure that the result is sane - all this checks is whether the code compiles and doesn't crash at runtime - who knows if `copy` points to the right thing after self-assignment? If you feel like this is worth checking, you could `expect (copy == it);` after each of the assignments.

---

## Comment 39

> Username: nbougalis  
> Created_at: 2016-05-25 12:16:09 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-221555942  

:+1:

---

## Comment 40

> Username: seelabs  
> Created_at: 2016-05-25 12:57:05 UTC  
> Updated_at: 2016-05-25 16:00:33 UTC  
> Url: https://github.com/boostorg/beast/pull/14#discussion_r64566852  

`hanshake` -> `handshake`

---

## Comment 41

> Username: seelabs  
> Created_at: 2016-05-25 13:09:25 UTC  
> Url: https://github.com/boostorg/beast/pull/14#issuecomment-221570637  

Still :+1: (though you should fix the minor typo I found).

---
