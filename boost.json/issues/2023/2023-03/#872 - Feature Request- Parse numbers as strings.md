# #872 - Feature Request: Parse numbers as strings [Open]

> Username: cryptochassis  
> Created at: 2023-03-20 21:57:27 UTC  
> Updated at: 2023-06-24 18:28:23 UTC  
> Url: https://github.com/boostorg/json/issues/872  

When working with various counterparties dealing with monetary systems, we found that, quite often than not, we'd recieve json strings like [1.2345] instead of ["1.2345"]. If we parse that as a double, then we might loose precisions in some cases. In order to preserve precision, we have to parse that number as a string. rapidjson offers a solution by providing kParseNumbersAsStringsFlag:  https://rapidjson.org/namespacerapidjson.html#a81379eb4e94a0386d71d15fda882ebc9a13981c0b803803f59d7a01aef3dfc987. Interesting enough, Python standard json library also offers the capability to parse numbers as strings:  
https://docs.python.org/3/library/json.html#json.load (see parse_float and parse_int parameters).  
We are looking into migrating to boost json library. Parsing numbers as strings is a key thing for us to preserve monetary precision. Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-03-20 21:58:47 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1476989915  

possible in theory, if we add it to the parse options. they will come in as strings. However, we can consider adding a flag to `json::string` somewhere (if we can find a spare bit) which indicates that the string contains a valid number. This should not affect performance if the option is not set.

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-03-21 06:39:34 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1477347969  

We technically already support this for _parsing_. Just use `basic_parser` with a custom handler. The caveat is that this is way more complicated than it should have been. We could make `detail::handler` public, and document how to override its functions to achieve custom handling of only a subset of parsing events.  
  
The more complicated part of the eqation is serialisation. We don't have a customisable serialiser. On the other hand, custom serialisation is very easy to implement with iostreams.  
  
So, no special bit for "this is actually a number" is required. BTW, I am sceptical that such change would not affect performance, even if only in a minor way.  
  
@cryptochassis do you only need this special handling for parsing? Is using basic_parser with a custom handler enough for you?

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-03-21 15:13:56 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1478018656  

Here's an example of what I meant: https://godbolt.org/z/KE7YK7h97

---

## Comment 4

> Username: cryptochassis  
> Created at: 2023-04-21 01:40:32 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1517138033  

@grisumbras Very sorry for the late reply. I completely missed your previous messages. Yes, we only need this special handling for parsing. Using basic_parser with a custom handler seems to be sufficient. Thanks a lot for providing a concrete example. One question: for the example, when the parser encounters a number, say, a double, will it still call std::stod behind the scene? Because we are a high-frequency-trading code provider, performance is of utmost importance to us. Without calling std::stod, I'd guess it'd save lots of CPU time.

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-04-25 10:52:50 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1521584287  

The number will still be parsed. But our parser doesn't call `std::strod` (or any other standard number parsing utility for that matter). We use custom number parsing functions, so maybe it will be fast enough for you.  
  
Also, this made me think we might want a parser option that disables number parsing outright.

---

## Comment 6

> Username: cryptochassis  
> Created at: 2023-04-26 13:21:32 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1523413718  

We parse about millions of json messages per second and therefore skipping string to number conversion would probably have visible impact on our system's performance. We'd appreciate if there could be provided a parser option that disables number parsing. Many thanks!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-04-26 13:33:08 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1523432163  

if you want the highest performance why don't you use simdjson? Do you need the ability to modify the JSON values?

---

## Comment 8

> Username: cryptochassis  
> Created at: 2023-04-27 23:06:03 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1526705951  

We don't need the ability to modify the JSON values. At the time that we first started our library development in 2019 and published its first version, simdjson wasn't available. Based on the best judgement at that time, we picked rapidjson. We ourselves is a library rather than an end-user application. The reason that we are now aiming at migrating to boost json instead of simdjson is because a sizable part of our current users (or those who are thinking about using our library) comes from a Python background and therefore are intermediate to beginner levels in C++. They need a simple way of getting started to build their applications using our library. The simplest way is to only rely on the header-only components of boost but nothing else. And we are getting closer to that: currently we only depend on boost, websocketpp, and rapidjson. We are almost there of moving away from websocketpp by using your beast websocket. So now the only thing to trim is rapidjson after which our only dependency are the header-only components of boost. To sum up, the reason is to achieve a good balance between performance and usability aiming at a wide array of audience having vastly different C++ proficiencies.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2023-04-28 01:11:32 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1526842163  

Wow... that rationale is actually rather perfect :)

---

## Comment 10

> Username: cryptochassis  
> Created at: 2023-06-20 14:14:38 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1598880126  

> The number will still be parsed. But our parser doesn't call `std::strod` (or any other standard number parsing utility for that matter). We use custom number parsing functions, so maybe it will be fast enough for you.  
>   
> Also, this made me think we might want a parser option that disables number parsing outright.  
  
Let me know whether we can have such a parser option. Thanks a lot.

---

## Comment 11

> Username: grisumbras  
> Created at: 2023-06-20 15:02:26 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1598969985  

An option to disable number parsing outright? I have a PR for that (#901). IIRC, my benchmarking shows that for number-heavy inputs the speed of parsing increases by 80% (but don't quote me on that). @vinniefalco should I pursue it?

---

## Comment 12

> Username: grisumbras  
> Created at: 2023-06-20 15:11:35 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1598986864  

To be clear, it still sort of does number validation (we need it to know when the number ends and the parser should start parsing another value), it just doesn't convert the characters into a number.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2023-06-21 11:56:45 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1600700448  

its an interesting mode

---

## Comment 14

> Username: cryptochassis  
> Created at: 2023-06-22 13:53:50 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1602682294  

> An option to disable number parsing outright? I have a PR for that (#901). IIRC, my benchmarking shows that for number-heavy inputs the speed of parsing increases by 80% (but don't quote me on that). @vinniefalco should I pursue it?  
  
Perfect. Looking forward to the finalization.  Thanks a lot.

---

## Comment 15

> Username: grisumbras  
> Created at: 2023-06-24 18:08:05 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1605673705  

#901 has been merged into develop

---

## Comment 16

> Username: grisumbras  
> Created at: 2023-06-24 18:28:23 UTC  
> Url: https://github.com/boostorg/json/issues/872#issuecomment-1605678548  

Local benchmarking results:  
```  
                        imprecise   | precise    | none	  
Parse gcc   apache_builds.json  754 | 753  -0,13%| 753  -0,13%  
Parse gcc   canada.json         587 | 400 -31,86%|1064  81,26%  
Parse gcc   citm_catalog.json   1231|1232   0,08%|1344   9,18%  
Parse gcc   github_events.json  837 | 845   0,96%| 850   1,55%  
Parse gcc   gsoc-2018.json      975 | 977   0,21%| 974  -0,10%  
Parse gcc   instruments.json    630 | 640   1,59%| 659   4,60%  
Parse gcc   marine_ik.json      531 | 404 -23,92%| 654  23,16%  
Parse gcc   mesh.json           532 | 402 -24,44%| 690  29,70%  
Parse gcc   mesh.pretty.json    996 | 758 -23,90%|1370  37,55%  
Parse gcc   numbers.json        818 | 494 -39,61%|1814 121,76%  
Parse gcc   random.json	gcc     383 | 384   0,26%| 385   0,52%  
Parse gcc   twitter.json        521 | 524   0,58%| 530   1,73%  
Parse gcc   twitterescaped.json 478 | 474  -0,84%| 488   2,09%  
Parse gcc   update-center.json  660 | 664   0,61%| 663   0,45%  
Parse clang apache_builds.json   757| 750  -0,92%| 751  -0,79%  
Parse clang canada.json          613| 378 -38,34%| 905  47,63%  
Parse clang citm_catalog.json   1225|1196  -2,37%|1234   0,73%  
Parse clang github_events.json   800| 793  -0,88%| 807   0,88%  
Parse clang gsoc-2018.json       721| 721   0,00%| 717  -0,55%  
Parse clang instruments.json     674| 653  -3,12%| 664  -1,48%  
Parse clang marine_ik.json       532| 400 -24,81%| 607  14,10%  
Parse clang mesh.json            557| 418 -24,96%| 708  27,11%  
Parse clang mesh.pretty.json    1086| 771 -29,01%|1373  26,43%  
Parse clang numbers.json         854| 524 -38,64%|1742 103,98%  
Parse clang random.json          377| 371  -1,59%| 372  -1,33%  
Parse clang twitter.json         556| 558   0,36%| 557   0,18%  
Parse clang twitterescaped.json  463| 470   1,51%| 468   1,08%  
Parse clang update-center.json   594| 597   0,51%| 594   0,00%  
```  
canada.json is +81% on GCC and +48% on clang,  numbers.json is +122% on GCC and +104% on clang.
