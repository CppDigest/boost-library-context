# #2578 Using extern template in source mode. [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-07 07:34:36 UTC  
> Updated at: 2023-02-17 05:54:44 UTC  
> Closed at: 2023-02-17 05:54:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2578  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-12-07 08:03:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1340550207  

![pullrequest](https://2578.beast.tracing.cppalliance.org/pullrequest-0c94468b.png)  
Timeline tracing charts: [https://2578.beast.tracing.cppalliance.org/index.html](https://2578.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-07 09:04:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1340616425  

![pullrequest](https://2578.beast.tracing.cppalliance.org/pullrequest-4c6abe98.png)  
Timeline tracing charts: [https://2578.beast.tracing.cppalliance.org/index.html](https://2578.beast.tracing.cppalliance.org/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-12-07 12:57:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2578#pullrequestreview-1208369796  

📁 CMakeLists.txt

```diff
  12 | add_library(boost_beast INTERFACE)
  13 |- add_library(Boost::beast ALIAS boost_beast)
  13 |+ add_library(Boost::beast ALIAS boost_beast include/boost/beast/core/impl/flat_buffer.ipp include/boost/beast/core/impl/multi_buffer.ipp include/boost/beast/core/impl/tcp_stream.ipp include/boost/beast/http/impl/dynamic_body.ipp include/boost/beast/http/impl/file_body.ipp include/boost/beast/http/impl/parser.ipp)
```

> Username: vinniefalco  
> Created_at: 2022-12-07 12:57:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#discussion_r1042172159  

this does not look right


---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2022-12-08 04:27:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1341981972  

| File | Master | `extern template` | Diff in %|  
|------|--------|---------------------|-----|  
lib_asio.json                    | 2055130  | 3197362  | -55.57954971218366%  
lib_asio_ssl.json                | 1169036  | 1100746  | 5.841565187043%  
lib_beast.json                   | 3699292  | 4117721  | -11.31105627779586%  
advanced_server_flex_awaitable.json | 2524398  | 2523951  | 0.017707191972105824%  
advanced_server_flex.json        | 34936881 | 30866071 | 11.651898748488739%  
advanced_server.json             | 10329014 | 11134455 | -7.797849823806996%  
http_client_async.json           | 7866590  | 6830071  | 13.176217395339021%  
http_client_coro.json            | 10148353 | 9270501  | 8.650191809449277%  
urls_large_data.json             | 483615   | 292403   | 39.53806230162423%  
http_crawl.json                  | 6509062  | 7086898  | -8.877408142678622%  
http_client_sync.json            | 6785110  | 4271589  | 37.04466102981382%  
http_client_awaitable.json       | 2042063  | 1895363  | 7.183911563942933%  
http_client_async_ssl.json       | 15309983 | 16058526 | -4.889247754226768%  
http_client_coro_ssl.json        | 12765077 | 14561526 | -14.07315443533948%  
http_client_sync_ssl.json        | 7417821  | 5313307  | 28.37105397932897%  
json_client.json                 | 7764130  | 4873263  | 37.233624372595514%  
http_server_async.json           | 5268686  | 5562621  | -5.5789052526569245%  
http_server_coro.json            | 9697143  | 8935795  | 7.851260933246008%  
http_server_fast.json            | 5501652  | 6237350  | -13.372310716853775%  
http_server_small.json           | 4889052  | 4581301  | 6.294696804206623%  
http_server_stackless.json       | 5222084  | 5801093  | -11.087699853162071%  
http_server_sync.json            | 5734644  | 3950402  | 31.113387334941805%  
http_server_async_ssl.json       | 10130232 | 7268380  | 28.25060669883967%  
http_server_coro_ssl.json        | 8487297  | 8917627  | -5.070283271576334%  
http_server_flex.json            | 8201978  | 10492393 | -27.92515415183996%  
http_server_stackless_ssl.json   | 10667189 | 7105591  | 33.388346264418864%  
http_server_sync_ssl.json        | 4876498  | 6061752  | -24.305433940504027%  
echo_op.json                     | 2754577  | 2661909  | 3.364146291789992%  
websocket_client_async.json      | 10690231 | 8895523  | 16.78829952318149%  
websocket_client_awaitable.json  | 3196185  | 3341105  | -4.534155563585963%  
websocket_client_coro.json       | 13157090 | 13065442 | 0.6965674020623102%  
websocket_client_sync.json       | 6556286  | 5086848  | 22.412658630206188%  
websocket_client_async_ssl.json  | 22408002 | 22775160 | -1.6385128848167723%  
websocket_client_async_ssl_system_executor.json | 21045494 | 28221748 | -34.09876717552935%  
websocket_client_coro_ssl.json   | 21231843 | 19610130 | 7.638116954802275%  
websocket_client_sync_ssl.json   | 7949249  | 7634494  | 3.959556431054053%  
websocket_server_async.json      | 14506288 | 11170061 | 22.9984886554024%  
websocket_server_awaitable.json  | 2236990  | 3554001  | -58.87424619689852%  
listener.json                    | 3705277  | 5541364  | -49.55329925400989%  
main.json                        | 4227498  | 3180320  | 24.77063265316743%  
shared_state.json                | 2760847  | 4140298  | -49.96477530265169%  
websocket_session.json           | 4823099  | 6701842  | -38.953025845001314%  
http_session.json                | 9803780  | 8547061  | 12.81871890230095%  
websocket_server_coro.json       | 15780066 | 15973133 | -1.2234866444791803%  
websocket_server_fast.json       | 20076936 | 14386942 | 28.340948041075592%  
websocket_server_stackless.json  | 9435270  | 12595036 | -33.488877371818724%  
websocket_server_sync.json       | 4498976  | 4947875  | -9.97780383802892%  
websocket_server_async_ssl.json  | 25933765 | 27056926 | -4.330882924249526%  
websocket_server_coro_ssl.json   | 26999063 | 26193336 | 2.984277639561047%  
websocket_server_stackless_ssl.json | 20654447 | 22795487 | -10.366000116100906%  
websocket_server_sync_ssl.json   | 5099601  | 5418447  | -6.252371509065122%  
lib_test.json                    | 2119549  | 2100414  | 0.9027863946528247%  
core.json                        | 1699039  | 2567839  | -51.13478854811455%  
http.json                        | 1712358  | 1736792  | -1.4269212396005977%  
ssl.json                         | 5096398  | 4232764  | 16.945968505599446%  
version.json                     | 29830    | 18322    | 38.57861213543413%  
websocket.json                   | 6039286  | 6161083  | -2.016745025819277%  
zlib.json                        | 827124   | 1261511  | -52.51776033581422%  
_test_detail_stream_state.json   | 977120   | 939639   | 3.83586458162764%  
error.json                       | 1294870  | 1820664  | -40.60592955277364%  
icy_stream.json                  | 2498130  | 3787510  | -51.61380712773155%  
stream.json                      | 5561579  | 7033480  | -26.46552355005656%  
_detail_base64.json              | 1247250  | 1317832  | -5.659009821607537%  
_detail_bind_continuation.json   | 824609   | 788923   | 4.327626790393993%  
_detail_buffer.json              | 1424882  | 1786158  | -25.35480130986285%  
_detail_clamp.json               | 1247693  | 1181023  | 5.34346189327022%  
_detail_get_io_context.json      | 2825160  | 1901304  | 32.70101516374294%  
_detail_is_invocable.json        | 441825   | 574806   | -30.098115769818367%  
_detail_read.json                | 1866309  | 2202741  | -18.02659688186683%  
_detail_sha1.json                | 1189355  | 1223362  | -2.859280870724048%  
_detail_tuple.json               | 1208980  | 1326776  | -9.74342007311949%  
_detail_variant.json             | 1338599  | 1349352  | -0.8033025573752857%  
_detail_varint.json              | 1229950  | 1174071  | 4.543192812715964%  
async_base.json                  | 2688531  | 3904501  | -45.228044608747304%  
basic_stream.json                | 9643588  | 8493679  | 11.92407846540105%  
bind_handler.json                | 3837715  | 3770215  | 1.7588591127793491%  
buffer_ref.json                  | 3213044  | 4074989  | -26.826430014652768%  
buffer_traits.json               | 1270799  | 1218697  | 4.09994027379625%  
buffered_read_stream.json        | 6167325  | 5108820  | 17.163113667594946%  
buffers_adaptor.json             | 1583726  | 1600735  | -1.0739862829807683%  
buffers_cat.json                 | 1910648  | 2026047  | -6.039783361456427%  
buffers_generator.json           | 4070685  | 2641750  | 35.10306005008985%  
buffers_prefix.json              | 1443495  | 2158859  | -49.55777470652825%  
buffers_range.json               | 1320034  | 2039589  | -54.51033837007229%  
buffers_suffix.json              | 2424980  | 1614654  | 33.415780748707206%  
buffers_to_string.json           | 1452630  | 1934213  | -33.15248893386478%  
detect_ssl.json                  | 3166281  | 3210408  | -1.3936539429065202%  
file_base.json                   | 1015501  | 1013034  | 0.24293427579096427%  
file.json                        | 912659   | 908563   | 0.44879851072525445%  
file_posix.json                  | 2020159  | 1649605  | 18.34281361021583%  
file_stdio.json                  | 2182744  | 2516064  | -15.270686805232314%  
file_win32.json                  | 30355    | 17712    | 41.650469444901994%  
filtering_cancellation_slot.json | 1851688  | 1172791  | 36.663682002583585%  
flat_buffer.json                 | 2326193  | 2636436  | -13.336941517750247%  
flat_static_buffer.json          | 1441051  | 1371974  | 4.793515288494301%  
flat_stream.json                 | 4013578  | 3639229  | 9.32706428030052%  
make_printable.json              | 1265987  | 1256101  | 0.7808926947907048%  
multi_buffer.json                | 2395070  | 2269917  | 5.225442262647856%  
ostream.json                     | 2088675  | 1370526  | 34.382994003375345%  
rate_policy.json                 | 1182730  | 1833538  | -55.0259146212576%  
read_size.json                   | 1378389  | 2003374  | -45.34169962180488%  
role.json                        | 28641    | 17697    | 38.21095632135749%  
saved_handler.json               | 2377302  | 1508902  | 36.52880450191015%  
span.json                        | 1253043  | 1696580  | -35.396790054291834%  
static_string.json               | 1814960  | 1925032  | -6.064706660201877%  
static_buffer.json               | 1523048  | 1881914  | -23.562356537679705%  
stream_traits.json               | 2285525  | 3026379  | -32.41504687106901%  
string.json                      | 632549   | 452458   | 28.470679741806563%  
tcp_stream.json                  | 2953612  | 2189387  | 25.87425159431909%  
basic_dynamic_body.json          | 1977069  | 1541333  | 22.039493816351378%  
basic_file_body.json             | 1417449  | 1540886  | -8.708390919179456%  
field.json                       | 1977264  | 1988666  | -0.576655418800929%  
basic_parser.json                | 4814568  | 3606543  | 25.09103620511747%  
buffer_body.json                 | 2807669  | 2738122  | 2.477037001156475%  
chunk_encode.json                | 3349041  | 2550767  | 23.835898097395642%  
dynamic_body.json                | 3905135  | 4230385  | -8.32877736621141%  
field_compiles.json              | 451950   | 496475   | -9.8517535125567%  
fields.json                      | 2856729  | 3294911  | -15.33859179502151%  
file_body.json                   | 3571002  | 4162868  | -16.57422762574762%  
message_generator.json           | 6315055  | 4256661  | 32.59502886356492%  
parser.json                      | 3495586  | 1919390  | 45.091037668648404%  
message.json                     | 2389011  | 2713080  | -13.564985678173938%  
read.json                        | 9632838  | 9328238  | 3.1621003073029987%  
rfc7230.json                     | 1459007  | 1300215  | 10.883566699817067%  
serializer.json                  | 3564015  | 3480743  | 2.3364660361979395%  
span_body.json                   | 2634925  | 1678179  | 36.31017960663017%  
status.json                      | 1267867  | 1734515  | -36.805753284847704%  
string_body.json                 | 1401443  | 1977385  | -41.096355684819144%  
type_traits.json                 | 1472041  | 1839546  | -24.965676907097016%  
vector_body.json                 | 1874125  | 1260545  | 32.73954512105649%  
verb.json                        | 1268016  | 1212392  | 4.386695436019735%  
write.json                       | 28328895 | 25169520 | 11.152482297668158%  
ssl_stream.json                  | 2201627  | 1403375  | 36.257367846597084%  
_detail_decorator.json           | 1697212  | 2604201  | -53.439935612050824%  
_detail_impl_base.json           | 2192464  | 1715990  | 21.732352275795634%  
_detail_prng.json                | 1974000  | 1268048  | 35.762512664640326%  
accept.json                      | 12562712 | 14286104 | -13.718311778539539%  
cancel.json                      | 9567924  | 9177664  | 4.078836746612954%  
close.json                       | 31976603 | 31395570 | 1.817056677346246%  
frame.json                       | 1667277  | 1620098  | 2.829703762482179%  
handshake.json                   | 16907016 | 15901175 | 5.949252073813617%  
option.json                      | 30313    | 21905    | 27.737274436710322%  
ping.json                        | 19642722 | 18147283 | 7.613196378791086%  
read1.json                       | 14379119 | 13308969 | 7.442389203399735%  
read2.json                       | 29073751 | 26595824 | 8.522900949382143%  
read3.json                       | 22925541 | 22886511 | 0.1702468002827065%  
rfc6455.json                     | 1752516  | 2158242  | -23.151058249967477%  
stream_base.json                 | 2720304  | 2615815  | 3.8410780559819786%  
stream_explicit.json             | 3934597  | 5637954  | -43.291778039784%  
stream_fwd.json                  | 19752    | 17923    | 9.25982179019846%  
teardown.json                    | 1099809  | 1134217  | -3.12854322886974%  
timer.json                       | 12235006 | 12318872 | -0.6854594104816949%  
utf8_checker.json                | 2454053  | 1482941  | 39.57176149007377%  
deflate_stream.json              | 2515879  | 1589847  | 36.80749352413212%  
inflate_stream.json              | 1539019  | 2383814  | -54.891784961719125%  
bench_buffers.json               | 1651719  | 1558678  | 5.632979943925086%  
bench_utf8_checker.json          | 1359337  | 1392599  | -2.4469281716013027%  
wsload-compile.json              | 7795949  | 9981576  | -28.035419421035208%  
bench_parser.json                | 2941865  | 2313664  | 21.353835067210767%  
nodejs_parser.json               | 207810   | 120367   | 42.07834079206968%  
core_snippets.json               | 2557829  | 2556365  | 0.057236038843878935%  
http_snippets.json               | 7742319  | 7561129  | 2.340254902956078%  
core_1_refresher.json            | 5114081  | 4969049  | 2.83593474565616%  
core_3_timeouts.json             | 14183106 | 13490630 | 4.882400230245758%  
core_4_layers.json               | 3750121  | 3787858  | -1.006287530455684%  
http_10_custom_parser.json       | 1318336  | 1360676  | -3.211624350696636%  
http_examples.json               | 10366628 | 8093714  | 21.925297213327227%  
websocket_1_connecting.json      | 4080424  | 4122401  | -1.0287411308236596%  
websocket_2_handshaking.json     | 6478449  | 5226349  | 19.3271568549818%  
websocket_3_decorator.json       | 5801046  | 5745588  | 0.956000004137185%  
websocket_4_messages.json        | 5180630  | 4622123  | 10.780677253538663%  
websocket_5_control_frames.json  | 4953549  | 5560453  | -12.251902625773965%  
websocket_6_timeouts.json        | 5138232  | 6256563  | -21.76489889907657%  
websocket_7_teardown.json        | 5118291  | 5495987  | -7.3793381423604085%  
websocket_8_notes.json           | 14278548 | 12697707 | 11.071440877601841%  
root_certificates.json           | 1990284  | 1267353  | 36.32300716882616%  
server_certificate.json          | 1025063  | 1609526  | -57.017276011328086%  
overall                          | 1014213237 | 991927343 | 2.197357832354933%

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2022-12-08 04:33:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1341989562  

The benchmark isn't reliable though, as you can see by the compilation of `lib_asio`.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2022-12-08 04:37:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1341999634  

Comparing those runs works though:   
  
https://2578.beast.tracing.cppalliance.org/index.html  
https://2562.beast.tracing.cppalliance.org/index.html  
  
e,g.  
  
https://2562.beast.tracing.cppalliance.org/example/echo-op/clang-linux-11/release/cxxstd-17-iso/threading-multi/visibility-hidden/echo_op.html  
https://2578.beast.tracing.cppalliance.org/example/echo-op/clang-linux-11/release/cxxstd-17-iso/threading-multi/visibility-hidden/echo_op.html  
  
4,037.349ms   
to   
3,856.813 ms  
  
isn't bad. ~3% gain. Similar to my overall, so 2-3% speedup.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2022-12-20 04:08:21 UTC  
> Updated_at: 2023-01-24 02:14:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1358815065  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2578](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (683d85e) into [develop](https://codecov.io/gh/boostorg/beast/commit/7453ce3260b7b08c842245a441a072ccd2bd4bf6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7453ce3) will **decrease** coverage by `0.55%`.  
> The diff coverage is `80.95%`.  
  
> :exclamation: Current head 683d85e differs from pull request most recent head 207036f. Consider uploading reports for the commit 207036f to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2578/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2578      +/-   ##  
===========================================  
- Coverage    94.61%   94.06%   -0.55%       
===========================================  
  Files          154      154                
  Lines        12062    12121      +59       
===========================================  
- Hits         11412    11402      -10       
- Misses         650      719      +69       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ZsYXRfYnVmZmVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/flat\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZmxhdF9idWZmZXIuaHBw) | `98.72% <ø> (ø)` | |  
| [include/boost/beast/core/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `93.75% <ø> (-0.19%)` | :arrow_down: |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `56.25% <ø> (-43.75%)` | :arrow_down: |  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `94.61% <ø> (ø)` | |  
| [include/boost/beast/http/impl/fields.ipp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGRzLmlwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/message.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL21lc3NhZ2UuaHBw) | `100.00% <ø> (+1.47%)` | :arrow_up: |  
| [include/boost/beast/http/parser.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3BhcnNlci5ocHA=) | `81.96% <ø> (ø)` | |  
| [include/boost/beast/http/string\_body.hpp](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL3N0cmluZ19ib2R5LmhwcA==) | `89.47% <ø> (ø)` | |  
| ... and [16 more](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [947b225...207036f](https://codecov.io/gh/boostorg/beast/pull/2578?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: glenfe  
> Created_at: 2023-01-24 13:47:57 UTC  
> Updated_at: 2023-01-24 13:49:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1401978359  

The gains don't seem impressive enough to make this worth it, in my opinion.  
  
Is there a plan to write a non-template version of Beast, i.e. with more of the storage_ptr style policies instead of template parameters?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2023-01-24 14:05:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2578#issuecomment-1402004450  

> Is there a plan to write a non-template version of Beast, i.e. with more of the storage_ptr style policies instead of template parameters?  
  
Yes: https://github.com/CPPAlliance/http_proto  
  
Although this new library doesn't provide any policies at all (which is another way of solving the problem).

---
