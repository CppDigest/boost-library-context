# #457 - Math size in Boost release [Open]

> Username: glenfe  
> Created at: 2020-12-17 15:53:53 UTC  
> Updated at: 2020-12-18 08:07:59 UTC  
> Url: https://github.com/boostorg/math/issues/457  

All of `libs` is 582M and `libs/math` is 158M of that. Any chance this could be reduced? Most of the weight is in `libs/math/doc` and `libs/math/reporting`.  
```  
582M	libs  
158M	libs/math  
28M	libs/json  
23M	libs/beast  
22M	libs/gil  
22M	libs/geometry  
20M	libs/spirit  
18M	libs/hana  
18M	libs/graph  
12M	libs/multiprecision  
12M	libs/log  
12M	libs/icl  
9.4M	libs/numeric  
9.2M	libs/asio  
9.0M	libs/test  
7.7M	libs/contract  
6.9M	libs/metaparse  
6.9M	libs/locale  
6.6M	libs/polygon  
6.2M	libs/mpl  
6.1M	libs/type_traits  
6.0M	libs/python  
6.0M	libs/fusion  
5.7M	libs/sort  
5.6M	libs/outcome  
5.5M	libs/algorithm  
5.2M	libs/compute  
5.0M	libs/msm  
4.8M	libs/fiber  
4.7M	libs/safe_numerics  
4.7M	libs/config  
3.9M	libs/static_string  
3.9M	libs/range  
3.9M	libs/iterator  
3.8M	libs/histogram  
3.7M	libs/regex  
3.6M	libs/thread  
3.3M	libs/vmd  
3.1M	libs/pool  
3.1M	libs/intrusive  
3.1M	libs/container  
3.0M	libs/wave  
3.0M	libs/preprocessor  
3.0M	libs/bimap  
2.9M	libs/phoenix  
2.8M	libs/hof  
2.6M	libs/optional  
2.4M	libs/serialization  
2.3M	libs/iostreams  
2.2M	libs/tti  
2.2M	libs/graph_parallel  
2.1M	libs/statechart  
2.1M	libs/smart_ptr  
2.1M	libs/leaf  
2.0M	libs/date_time  
1.9M	libs/multi_index  
1.9M	libs/context  
1.8M	libs/interprocess  
1.7M	libs/filesystem  
1.6M	libs/proto  
1.6M	libs/mp11  
1.6M	libs/local_function  
1.6M	libs/convert  
1.5M	libs/random  
1.4M	libs/nowide  
1.4M	libs/core  
1.3M	libs/qvm  
1.3M	libs/parameter  
1.3M	libs/callable_traits  
1.2M	libs/unordered  
1.2M	libs/poly_collection  
1.2M	libs/exception  
1.1M	libs/units  
1.1M	libs/ptr_container  
1.1M	libs/chrono  
1005K	libs/coroutine  
927K	libs/mpi  
924K	libs/xpressive  
867K	libs/accumulators  
810K	libs/yap  
806K	libs/utility  
803K	libs/endian  
775K	libs/bind  
708K	libs/coroutine2  
702K	libs/flyweight  
700K	libs/type_erasure  
671K	libs/winapi  
666K	libs/move  
661K	libs/program_options  
644K	libs/process  
597K	libs/circular_buffer  
576K	libs/multi_array  
527K	libs/variant2  
510K	libs/atomic  
491K	libs/function_types  
484K	libs/variant  
468K	libs/predef  
467K	libs/signals2  
446K	libs/property_tree  
445K	libs/dll  
437K	libs/system  
414K	libs/ratio  
413K	libs/scope_exit  
412K	libs/lambda  
401K	libs/concept_check  
399K	libs/functional  
384K	libs/stl_interfaces  
383K	libs/integer  
338K	libs/container_hash  
330K	libs/assign  
323K	libs/lexical_cast  
282K	libs/function  
270K	libs/heap  
261K	libs/pfr  
248K	libs/uuid  
241K	libs/type_index  
239K	libs/dynamic_bitset  
225K	libs/tuple  
220K	libs/detail  
209K	libs/format  
206K	libs/property_map  
195K	libs/stacktrace  
179K	libs/lockfree  
171K	libs/crc  
169K	libs/assert  
160K	libs/io  
157K	libs/timer  
155K	libs/rational  
150K	libs/throw_exception  
142K	libs/foreach  
137K	libs/tokenizer  
115K	libs/typeof  
107K	libs/static_assert  
103K	libs/align  
101K	libs/logic  
101K	libs/array  
93K	libs/any  
78K	libs/parameter_python  
63K	libs/conversion  
17K	libs/compatibility  
5.0K	libs/headers  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2020-12-17 15:54:49 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747527699  

The sizes of things in `libs/math`:  
  
```  
158M	libs/math  
63M	libs/math/doc  
60M	libs/math/reporting  
34M	libs/math/test  
1.5M	libs/math/example  
728K	libs/math/tools  
544K	libs/math/dot_net_example  
363K	libs/math/src  
80K	libs/math/include_private  
48K	libs/math/minimax  
27K	libs/math/config  
5.0K	libs/math/vc71_fix  
4.0K	libs/math/meta  
4.0K	libs/math/build  
  
```

---

## Comment 2

> Username: pabristow  
> Created at: 2020-12-17 16:59:50 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747568310  

I was wondering if anyone would notice 😉  
  
One contributor is the size of some graphics and I am working on changing the plot program to help with this, but not in time for this release.  
  
But I suspect most of the cause is the steady increase in the number of functions and distributions provided.  
  
But I am sure that all Boost.Math contributors note that size is an issue and will look at ways of reducing it.  
  
Perhaps we need to keep some of the testing code and results separate from the Boost download?  
  
Sorry, but I don’t see a quick fix for this.  
  
Paul  
  
  
From: Glen Fernandes <notifications@github.com>  
Sent: 17 December 2020 15:55  
To: boostorg/math <math@noreply.github.com>  
Cc: Subscribed <subscribed@noreply.github.com>  
Subject: Re: [boostorg/math] Math size in Boost release (#457)  
  
  
The sizes of things in libs/math:  
  
158M    libs/math  
  
63M     libs/math/doc  
  
60M     libs/math/reporting  
  
34M     libs/math/test  
  
1.5M    libs/math/example  
  
728K    libs/math/tools  
  
544K    libs/math/dot_net_example  
  
363K    libs/math/src  
  
80K     libs/math/include_private  
  
48K     libs/math/minimax  
  
27K     libs/math/config  
  
5.0K    libs/math/vc71_fix  
  
4.0K    libs/math/meta  
  
4.0K    libs/math/build  
  
  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/math/issues/457#issuecomment-747527699>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4AMI5LBMDODNYJTKX2LSVISVTANCNFSM4U73S3TQ>.

---

## Comment 3

> Username: apolukhin  
> Created at: 2020-12-17 17:45:53 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747594203  

On Thu, Dec 17, 2020, 20:00 Paul A. Bristow <notifications@github.com>  
wrote:  
  
> Sorry, but I don’t see a quick fix for this.  
  
  
Some ideas:  
* Try to remove all the generated files and generate them again?.  
  
Doxygen may change the file name on minor changes. So during the  
development phase a bunch of duplicate files could appear.  
  
* How about keeping the generated docs in a separate branch (gh-pages for  
example)?

---

## Comment 4

> Username: glenfe  
> Created at: 2020-12-17 17:56:01 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747599858  

It's fine if the reduction can't make the next release, but to know that someone is now actively working to help address it is good enough for me.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-12-17 18:30:10 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747618486  

I've pulled some low-hanging fruit (files that shouldn't have been in there in the first place as they're too large) and chopped it down to 104Mb.  Better but still not great.  
  
The rest is down to volume of stuff, rather than individual culprits.  
  
* There's 27Mb of test data under /test/, but the users can't build the tests without them.  
* There's 11Mb of generated html files, but I'm wary of moving to "online only" documentation.  
* There's 21Mb of images for the docs, again we could go online only I guess.

---

## Comment 6

> Username: glenfe  
> Created at: 2020-12-17 20:20:40 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747679447  

Many thanks (as always) John.  
  
Maybe ultimately we need to have two separate archives, one for the Boost library sources, and one for Boost library documentation. (Doc sources can go in the former, but generated documentation only in the latter).

---

## Comment 7

> Username: Flamefire  
> Created at: 2020-12-18 08:07:59 UTC  
> Url: https://github.com/boostorg/math/issues/457#issuecomment-747936197  

> There's 11Mb of generated html files, but I'm wary of moving to "online only" documentation.  
> There's 21Mb of images for the docs, again we could go online only I guess.  
  
Why not online? I mean we have a live version of the generated boost docs created by CI, so I don't see a reason to keep generated files in the repo. If a user needs it locally thy can generate them themselves. And if that alone chops of 32MB of data, then I'd say that is well worth it.
