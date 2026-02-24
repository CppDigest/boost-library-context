# #1102 - Comparison to Glaze [Open]

> Username: vinniefalco  
> Created at: 2025-08-28 02:39:32 UTC  
> Updated at: 2025-09-15 09:30:46 UTC  
> Url: https://github.com/boostorg/json/issues/1102  

We need to evaluate the performance of Boost.JSON against Glaze. Pure DOM (`json::value`) and to/from struct (apples to apples).

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-08-29 06:30:17 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3235885548  

I actually experimented with it, but then dropped it for some reason I already forgot.

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-08-29 16:16:28 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3237565029  

Local results  
```  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (direct),363  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (pool),369  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,glaze,1381  
  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (direct),496  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (pool),353  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,glaze,825  
  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (direct),640  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (pool),788  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,glaze,1880  
  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (direct),1567  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (pool),307  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,glaze,4908  
  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (direct),491  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (pool),209  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,glaze,610  
  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (direct),2275  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (pool),539  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,glaze,8275  
```  
  
Note that I haven't checked that Glaze produces correct outputs yet.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-08-29 19:58:52 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3238107450  

does glaze have a DOM?

---

## Comment 4

> Username: grisumbras  
> Created at: 2025-09-01 11:13:17 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3241967746  

Kinda. Their value type is a wrapper over `std::variant` of `std::nullpr_t`, bool, double, `std::vector`, and `std::map`. I'm adding it to the benchmark too.

---

## Comment 5

> Username: kelbon  
> Created at: 2025-09-12 04:33:53 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3283621859  

> Local results  
>   
> ```  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (direct),363  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (pool),369  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,glaze,1381  
>   
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (direct),496  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (pool),353  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,glaze,825  
>   
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (direct),640  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (pool),788  
> Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,glaze,1880  
>   
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (direct),1567  
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (pool),307  
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,glaze,4908  
>   
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (direct),491  
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (pool),209  
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,glaze,610  
>   
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (direct),2275  
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (pool),539  
> Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,glaze,8275  
> ```  
>   
> Note that I haven't checked that Glaze produces correct outputs yet.  
  
@grisumbras what the numbers mean?  
  
I recently saw a mention of the Glaze library, looked at their benchmark and to be honest its worst benchmark i've seen in a while.  
  
Their benchmark looks so fake that I'm curious about the real numbers.  
  
On the other hand, I know an implementation on top of the Boost Json that does direct stream parsing into values ​​and beats the native  Boost Json implementation, maybe later I'll give you the code to add to the benchmark

---

## Comment 6

> Username: grisumbras  
> Created at: 2025-09-12 10:51:40 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3284792557  

> [@grisumbras](https://github.com/grisumbras) what the numbers mean?  
  
Megabytes per second. `boost (direct)` is using Boost.JSON with direct operations.  `boost (pool)` is using Boost.JSON with DOM, `monotonic_resource`, and `value_to/from`.  
  
Glaze is indeed very fast when you use it with direct operations (how it was intended to be used).   
  
> On the other hand, I know an implementation on top of the Boost Json that does direct stream parsing into values ​​and beats the native Boost Json implementation, maybe later I'll give you the code to add to the benchmark  
  
Can you give a link? If it doesn't break `parse_into` API, I may integrate it into the library myself.  
  
Now, these are the benchmarks for JSON DOM and Glaze "DOM" (`glz::json_t`).  
  
```  
  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost,425  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (pool),596  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,glaze,275  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost,356  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (pool),547  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,glaze,229  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost,543  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (pool),1177  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,glaze,328  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/github_events.json,gcc x64/sse2,boost,375  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/github_events.json,gcc x64/sse2,boost (pool),825  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/github_events.json,gcc x64/sse2,glaze,254  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/gsoc-2018.json,gcc x64/sse2,boost,831  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/gsoc-2018.json,gcc x64/sse2,boost (pool),1814  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/gsoc-2018.json,gcc x64/sse2,glaze,591  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/instruments.json,gcc x64/sse2,boost,308  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/instruments.json,gcc x64/sse2,boost (pool),654  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/instruments.json,gcc x64/sse2,glaze,215  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/marine_ik.json,gcc x64/sse2,boost,289  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/marine_ik.json,gcc x64/sse2,boost (pool),534  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/marine_ik.json,gcc x64/sse2,glaze,120  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/mesh.json,gcc x64/sse2,boost,459  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/mesh.json,gcc x64/sse2,boost (pool),580  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/mesh.json,gcc x64/sse2,glaze,214  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/mesh.pretty.json,gcc x64/sse2,boost,911  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/mesh.pretty.json,gcc x64/sse2,boost (pool),1106  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/mesh.pretty.json,gcc x64/sse2,glaze,425  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/numbers.json,gcc x64/sse2,boost,791  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/numbers.json,gcc x64/sse2,boost (pool),877  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/numbers.json,gcc x64/sse2,glaze,446  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/random.json,gcc x64/sse2,boost,223  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/random.json,gcc x64/sse2,boost (pool),347  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/random.json,gcc x64/sse2,glaze,134  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/twitter.json,gcc x64/sse2,boost,317  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/twitter.json,gcc x64/sse2,boost (pool),526  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/twitter.json,gcc x64/sse2,glaze,212  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/twitterescaped.json,gcc x64/sse2,boost,278  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/twitterescaped.json,gcc x64/sse2,boost (pool),461  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/twitterescaped.json,gcc x64/sse2,glaze,186  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/update-center.json,gcc x64/sse2,boost,322  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/update-center.json,gcc x64/sse2,boost (pool),555  
Parse /home/grisumbras/dev/boost/libs/json/bench/data/update-center.json,gcc x64/sse2,glaze,167  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost,1635  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,boost (pool),1667  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/apache_builds.json,gcc x64/sse2,glaze,1524  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost,548  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,boost (pool),550  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/canada.json,gcc x64/sse2,glaze,510  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost,1990  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,boost (pool),2093  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/citm_catalog.json,gcc x64/sse2,glaze,1432  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/github_events.json,gcc x64/sse2,boost,1697  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/github_events.json,gcc x64/sse2,boost (pool),1705  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/github_events.json,gcc x64/sse2,glaze,1893  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/gsoc-2018.json,gcc x64/sse2,boost,1503  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/gsoc-2018.json,gcc x64/sse2,boost (pool),1547  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/gsoc-2018.json,gcc x64/sse2,glaze,1886  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/instruments.json,gcc x64/sse2,boost,1391  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/instruments.json,gcc x64/sse2,boost (pool),1411  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/instruments.json,gcc x64/sse2,glaze,1263  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/marine_ik.json,gcc x64/sse2,boost,461  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/marine_ik.json,gcc x64/sse2,boost (pool),481  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/marine_ik.json,gcc x64/sse2,glaze,305  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/mesh.json,gcc x64/sse2,boost,413  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/mesh.json,gcc x64/sse2,boost (pool),413  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/mesh.json,gcc x64/sse2,glaze,371  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/mesh.pretty.json,gcc x64/sse2,boost,880  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/mesh.pretty.json,gcc x64/sse2,boost (pool),887  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/mesh.pretty.json,gcc x64/sse2,glaze,807  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/numbers.json,gcc x64/sse2,boost,427  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/numbers.json,gcc x64/sse2,boost (pool),426  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/numbers.json,gcc x64/sse2,glaze,514  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/random.json,gcc x64/sse2,boost,880  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/random.json,gcc x64/sse2,boost (pool),938  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/random.json,gcc x64/sse2,glaze,516  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/twitter.json,gcc x64/sse2,boost,1598  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/twitter.json,gcc x64/sse2,boost (pool),1619  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/twitter.json,gcc x64/sse2,glaze,1369  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/twitterescaped.json,gcc x64/sse2,boost,1423  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/twitterescaped.json,gcc x64/sse2,boost (pool),1450  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/twitterescaped.json,gcc x64/sse2,glaze,1204  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/update-center.json,gcc x64/sse2,boost,1040  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/update-center.json,gcc x64/sse2,boost (pool),1086  
Serialize /home/grisumbras/dev/boost/libs/json/bench/data/update-center.json,gcc x64/sse2,glaze,727  
```  
  
In this case Glaze parses much slower than Boost.JSON using the default memory resource (which is in turn much slower than when `monotonic_reqource` is used). Difference in serialization speed is less pronounced. Glaze even outperforms Boost.JSON on `numbers.json` (that file is a giant array of numbers). This is due to it directly using Dragnobox. I'm considering doing the same for Boost.JSON.  
  
The most obvious conclusion is that Boost.JSON is absolutely superior if you need a DOM. If you can avoid a DOM, Glaze will probably always be faster. There are still other benefits Boost.JSON has, like actual documentation and streaming I/O support.

---

## Comment 7

> Username: kelbon  
> Created at: 2025-09-12 13:46:14 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3285365165  

> Can you give a link? If it doesn't break parse_into API, I may integrate it into the library myself.  
  
First of all, i see you use only gcc in your benchmark, it may be useful to add clang into comparison. On my benchmark, there are cases when gcc 5 times worse than clang  
  
About different solution for direct parsing - it uses parse api from Boost Json, key idea to use recusrive generator coroutine for implementing parsing for all types  
  
So, there are stream_parser:  
  
```cpp  
struct stream_parser {  
  boost::json::basic_parser<boostjson_sax_producer> p;  
  
  template <typename T>  
  explicit stream_parser(T& t, std::span<byte_t> buf = {});  
  void feed(std::string_view data, bool end, io_error_code& ec) {  
    p.write_some(!end, data.data(), data.size(), ec);  
    ...  
  }  
};  
  
```  
and `boostjson_sax_producer` here [implemented on top of recursive generator](https://github.com/bot-motherlib/TGBM/blob/master/include/tgbm/jsons/boostjson_sax_producer.hpp)  
  
Then, parsing for concrete type implemented as coroutine, e.g. [for vector of T](https://github.com/bot-motherlib/TGBM/blob/master/include/tgbm/jsons/parse_sax/vector_parser.hpp):  
  
```cpp  
  generator_type parse(std::vector<T>& v, sax_token& tok, dd::with_stack_resource r) {  
    tok.expect(sax_token::array_begin);  
    for (;;) {  
      co_yield {};  
      if (tok.got == sax_token::array_end)  
        break;  
      co_yield dd::elements_of(parser_for<T>(v.emplace_back(), tok, r));  
    }  
  }  
```

---

## Comment 8

> Username: grisumbras  
> Created at: 2025-09-15 09:30:46 UTC  
> Url: https://github.com/boostorg/json/issues/1102#issuecomment-3291244966  

I did run benchmarks with Clang too. The results are in the same ballpark. Interestingly, Clang is consistently slower for most benchmarks for me (but not by much).  
  
> About different solution for direct parsing - it uses parse api from Boost Json, key idea to use recusrive generator coroutine for implementing parsing for all types  
  
This is *very* interesting. I obviosuly cannot make it the default implementation in a C++11 library, but this can be a great example in the docs or even an alternative implementation.
