# #1801 - Compile Error GCC 4.9.4 (stream.hpp) [Closed]

> Username: jameschicca  
> Created at: 2020-01-14 22:09:28 UTC  
> Updated at: 2020-02-21 21:15:24 UTC  
> Closed at: 2020-02-21 21:15:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1801  

Ubuntu 14.04  
GCC 4.9.4  
Boost 1.72  
  
I have taken your sample and built it against boost 1.72 with gcc 4.9.4. According to boost documentation, I believe this should compile but I am receiving an error. The sample I am using is https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi  
  
Thoughts on why it doesn't compile? Thanks in advance.  
  
```In file included from ext/linux/x86-64/release/include/boost_1.72.0/boost/beast/websocket.hpp:18:0,  
                 from ext/linux/x86-64/release/include/boost_1.72.0/boost/beast.hpp:18,  
                 from ./misc/beastsocket/./common/include/beast_common/beast.h:13,  
                 from ./misc/beastsocket/./server/include/beast_server/websocket_session.h:14,  
                 from /home/repos/misc/beastsocket/server/source/websocket_session.cpp:10:  
ext/linux/x86-64/release/include/boost_1.72.0/boost/beast/websocket/stream.hpp: In instantiation of ‘class boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >’:  
./misc/beastsocket/./server/include/beast_server/websocket_session.h:30:42:   required from here  
ext/linux/x86-64/release/include/boost_1.72.0/boost/beast/websocket/stream.hpp:883:5: internal compiler error: in tsubst, at cp/pt.c:11743  
     async_handshake(  
     ^  
0x5ed04c tsubst(tree_node*, tree_node*, int, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:11743  
0x5f1442 tsubst_template_args  
	../../../gcc-4.9.4/gcc/cp/pt.c:10018  
0x5ebf9b tsubst(tree_node*, tree_node*, int, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:11939  
0x5ec50b tsubst(tree_node*, tree_node*, int, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:11589  
0x5f1442 tsubst_template_args  
	../../../gcc-4.9.4/gcc/cp/pt.c:10018  
0x5ebf9b tsubst(tree_node*, tree_node*, int, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:11939  
0x5ec32d tsubst(tree_node*, tree_node*, int, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:11581  
0x5ed615 tsubst_template_parm  
	../../../gcc-4.9.4/gcc/cp/pt.c:10149  
0x5ed615 tsubst_template_parms  
	../../../gcc-4.9.4/gcc/cp/pt.c:10110  
0x5f3629 tsubst_decl  
	../../../gcc-4.9.4/gcc/cp/pt.c:10474  
0x5ec4af tsubst(tree_node*, tree_node*, int, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:11555  
0x5fddd1 instantiate_class_template_1  
	../../../gcc-4.9.4/gcc/cp/pt.c:9135  
0x5fddd1 instantiate_class_template(tree_node*)  
	../../../gcc-4.9.4/gcc/cp/pt.c:9449  
0x656b8d complete_type(tree_node*)  
	../../../gcc-4.9.4/gcc/cp/typeck.c:134  
0x5d855e grokdeclarator(cp_declarator const*, cp_decl_specifier_seq*, decl_context, int, tree_node**)  
	../../../gcc-4.9.4/gcc/cp/decl.c:10594  
0x61631c grokfield(cp_declarator const*, cp_decl_specifier_seq*, tree_node*, bool, tree_node*, tree_node*)  
	../../../gcc-4.9.4/gcc/cp/decl2.c:852  
0x62929e cp_parser_member_declaration  
	../../../gcc-4.9.4/gcc/cp/parser.c:20556  
0x62cae0 cp_parser_member_specification_opt  
	../../../gcc-4.9.4/gcc/cp/parser.c:20105  
0x62cae0 cp_parser_class_specifier_1  
	../../../gcc-4.9.4/gcc/cp/parser.c:19333  
0x62cae0 cp_parser_class_specifier  
	../../../gcc-4.9.4/gcc/cp/parser.c:19560  
Please submit a full bug report,  
with preprocessed source if appropriate.  
Please include the complete backtrace with any bug report.  
See <http://gcc.gnu.org/bugs.html> for instructions.  
make: *** [beastsocket/server/source/websocket_session.o] Error 1  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-14 22:23:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574402485  

It says it right in the error message:  
  
> internal compiler error  
  
The problem is in the compiler, or the environment. Maybe you don't have enough RAM?

---

## Comment 2

> Username: jameschicca  
> Created at: 2020-01-15 02:34:54 UTC  
> Updated at: 2020-01-15 02:58:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574467892  

@vinniefalco lol, yes. thank you. Here I thought it was   
> error compiler internal  
   
;)  
  
But, yeah, I guess that is my question/concern. I am not sure why the compiler would barf when it should be compatible. The RAM aspect is interesting due to the template aspect of the compile. I monitored the RAM of the build and it didn't seem to spike prior to the failure. For safety I will try another box and see if I get a different result. (will report back tomorrow) If that doesn't work, do you have other thoughts? Are my assumptions correct that boost/beast/your_samples should be 4.9.4 gcc compatible?  
  
Also, it's a pleasure to meet you. I watched all of your videos and really enjoyed them.  
Thanks again for your time and consideration in this matter.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-15 04:28:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574490554  

> Are my assumptions correct that boost/beast/your_samples should be 4.9.4 gcc compatible?  
  
Yes. they should compile. gcc 4.8.4 is tested. Make sure you're using an official release and not a beta.  
  
> do you have other thoughts?  
  
Check your swap space?

---

## Comment 4

> Username: jameschicca  
> Created at: 2020-01-15 16:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574754056  

I tested this with 12cpu, 16Gig mem. Still fails compilation.   
To confirm, this should work with 4.9.4 and not 4.8.4, right? You mentioned 4.8.4 and I am not sure if that was a mistake.   
I did confirm that this builds with:  
> g++ --version  
> g++ (GCC) 7.3.1 20180303 (Red Hat 7.3.1-5)  
  
So I am concerned that 4.9.4 might not be compatible. Do you have the ability to compile your sample in 4.9.4 to confirm if we are doing something wrong?

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-01-15 18:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574782850  

Confirmed. This is a bug in gcc-4.9  
  
```  
$ g++-4.9 -c -I /src/boost_1_72_0 -std=c++1y websocket_session.cpp -o websocket_session.cpp.o  
In file included from /src/boost_1_72_0/boost/beast/websocket.hpp:18:0,  
                 from /src/boost_1_72_0/boost/beast.hpp:18,  
                 from beast.hpp:13,  
                 from websocket_session.hpp:14,  
                 from websocket_session.cpp:10:  
/src/boost_1_72_0/boost/beast/websocket/stream.hpp: In instantiation of 'class boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >':  
websocket_session.hpp:30:42:   required from here  
/src/boost_1_72_0/boost/beast/websocket/stream.hpp:883:5: internal compiler error: in tsubst, at cp/pt.c:11743  
     async_handshake(  
     ^  
Please submit a full bug report,  
with preprocessed source if appropriate.  
See <file:///usr/share/doc/gcc-4.9/README.Bugs> for instructions.  
Preprocessed source stored into /tmp/cccFUgD6.out file, please attach this to your bugreport.  
```  
  
but not in 4.8  
  
```  
$ g++-4.8 -c -I /src/boost_1_72_0 -std=c++1y websocket_session.cpp -o websocket_session.cpp.o  
$ echo $?  
0  
```

---

## Comment 6

> Username: jameschicca  
> Created at: 2020-01-15 18:19:23 UTC  
> Updated at: 2020-01-15 18:56:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574786981  

Well at least I am not going insane but this is very unfortunate to hear. Do we know of any modifications to the template structure/declaration that might allow the compiler to not barf? I am not sure if other areas of asio have encountered this and have restructured accordingly.   
  
UPDATE  
I started pulling apart the declaration and it looks like it is `default_completion_token` that is causing it  
```  
ext/linux/x86-64/release/include/boost_1.72.0/boost/beast/websocket/stream.hpp:883:73: internal compiler error: in tsubst, at cp/pt.c:11743  
     typedef typename net::default_completion_token<executor_type>::type def_compl;  
                                                                         ^  
```

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-01-15 18:57:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574802027  

@jameschicca   
gcc 4.9 is known to be very buggy. Is there a particular reason that you have to use it?

---

## Comment 8

> Username: jameschicca  
> Created at: 2020-01-15 19:00:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574803405  

@madmongo1 My place of work is currently locked to that version. I have been trying to get them to upgrade but it is like steering the titanic with a fork... We were actually 4.9.1 and I had us bumped to 4.9.4 since boost (1.72) states that they are compatible with that version (https://www.boost.org/users/history/version_1_72_0.html). I was hoping that would resolve our compilation issue.

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-01-15 19:20:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574814403  

The error above initiates on line 879:  
```  
template<  
        BOOST_BEAST_ASYNC_TPARAM1 HandshakeHandler =  
            net::default_completion_token_t<executor_type>  
    >  
```  
`net::default_completion_token_t` is defined in asio. The `net` namespace is a namespace alias.  
  
This surfaces two uncomfortable truths:  
  
1) The compiler error is occurring in code defined in `boost::asio`. This library is maintained by Chris Kohlhoff who also maintains standalone asio and is driving the net.ts work. If I had to guess, a request to ship a patch that would work on 4.9 would be at best sympathetically received and then binned.  
  
2) Your employer would be wise to allow you to up or downgrade away from gcc-4.9, not only because of this compiler bug, but also in order to avoid shipping nondeterministic executables.  
  
It might be interesting to note that getting gcc-4.9 installed on my ubuntu trusty container involved a number of google searches. 4.9 only made it as far as the ubuntu test repository. It was never released. I don't doubt this will be in part due to its unreliability as a compiler tool.  
  
I wish I had better news.

---

## Comment 10

> Username: jameschicca  
> Created at: 2020-01-15 19:31:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574818778  

@madmongo1 Yes, I discovered the location of `net` namespace after posting. `Option 1` sounded great up to the ending ;).   
  
I have a meeting setup tomorrow to discuss this further with our group. Thanks again for your time and consideration in this matter. The sanity check was very helpful as the boost documentation listing 4.9.4 was extremely misleading. "It builds" is very different than "template instantiation tested" :)  
  
I will update the ticket tomorrow after the meeting with the direction we would like to pursue. Thanks again

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-01-15 19:48:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574827133  

@jameschicca Of course I can understand being misled by the possible ambiguity of the wording.  
  
I've made a report of that in the #boost cpplang slack channel (which is well work joining if you work with c++).  
  
If I can help you in your meeting tomorrow, I'd like to draw your attention to the boost beast documentation:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/introduction.html#beast.introduction.requirements  
  
> Beast requires:  
> C++11: *Robust* support for most language features.  
  
gcc-4.9 has by no means "robust" support for c++11.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-01-15 20:33:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-574843868  

@jameschicca I have received confirmation that the Boost documentation will be updated to be clearer as a direct result of you raising this issue. On behalf of the whole team, thank you.

---

## Comment 13

> Username: stale[bot]  
> Created at: 2020-02-14 20:42:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-586464958  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2020-02-21 21:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1801#issuecomment-589837898  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
