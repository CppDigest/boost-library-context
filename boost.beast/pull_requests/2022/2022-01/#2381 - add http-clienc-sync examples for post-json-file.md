# #2381 add http-clienc-sync examples for post/json/file [Closed]

> Username: schvarcz  
> Created at: 2022-01-20 18:42:33 UTC  
> Updated at: 2023-12-27 15:40:42 UTC  
> Closed at: 2023-12-27 15:40:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2381  

Hello everyone.  
  
According discussed on the [issue 301](https://github.com/boostorg/beast/issues/301), having a `http-client-sync-post` example would be extremely useful to new users. It seems to be a very old request that is still on demand.  
  
This PR aims to provide a few examples. According suggested and requested on the [issue 301](https://github.com/boostorg/beast/issues/301).  
  
Please, let me know if you would prefer the files organised on another way. Particularly about the Jamfile. I haven't had the need to use that before.  
  
Kind regards

---

## Comment 1

> Username: sehe  
> Created_at: 2022-06-07 00:30:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1148067181  

There's no such thing as "performing a HTTP JSON". It's just post. The JSON example only differs in two lines:  
  
```c++  
std::string payload = "var1=1&var2=2";  
req.set(http::field::content_type, "application/x-www-form-urlencoded");  
```  
  
Became  
  
```c++  
std::string payload = "{\"m_list\":[1,2,3]}";  
req.set(http::field::content_type, "application/json");  
```  
This will just make it really hard for users to see what *exactly* is required/different. I suggest merging the examples.  
  
To a lesser extent the same goes for the `_get` flavoured copy of the client example: it's quite literally just the post example with `s/post/get/` and the body removed.  
  
All examples with a body are missing `prepare_payload` to DoTheRightThing(TM) regardless of HTTP/1.1 vs HTTP/1.0 in relation to the content-length. Setting headers to non-string values no longer does the implicit conversion.  
  
The multipart upload seems nice, I've added a few review comments there. I've rebased the PR branch on latest develop and incorporated the changes outlined in the review comments, except the merging of `get`/`post-json`/`post-form` examples. I'm open to suggestions on how to approach that.

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-07 00:54:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-997386339  

📁 example/http/client/sync/Jamfile

```diff
  11 |-     http_client_sync.cpp
  10 |+ exe http-client-sync-get :
  11 |+     http_client_sync_get.cpp
```

> Username: vinniefalco  
> Created_at: 2022-06-07 00:54:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890675231  

If you rename this file then it will break any page that links to it

> Username: sehe  
> Created_at: 2022-06-07 01:04:47 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890678861  

Just pushed fa4716572a - which leaves the decision on how to organize the new example programs _to be done_


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-06-07 01:02:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1148083921  

![pullrequest](https://2381.beast.tracing.cppalliance.org/pullrequest-e76cfe61.png)  
Timeline tracing charts: [https://2381.beast.tracing.cppalliance.org/index.html](https://2381.beast.tracing.cppalliance.org/index.html)

---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-07 01:04:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-997390410  

📁 example/http/client/sync/http_client_sync_post_json.cpp

```diff
  21 |+ #include <boost/beast/http.hpp>
  22 |+ #include <boost/beast/version.hpp>
  23 |+ #include <boost/json/src.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-06-07 01:04:42 UTC  
> Updated_at: 2022-06-07 01:04:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890678823  

hmm idk about this, the CML / Jamfile should link to the actual library. we should not include src.hpp like this. And it can confuse automation tools which try to figure out library dependencies.

> Username: sehe  
> Created_at: 2022-06-07 01:24:49 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890686163  

Did that. Will resolve this one, since I've asked a new question about the CML change


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-06-07 01:11:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1148088212  

An automated preview of the documentation is available at [https://2381.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2381.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-06-07 01:22:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1148092851  

![pullrequest](https://2381.beast.tracing.cppalliance.org/pullrequest-d0c9fa5d.png)  
Timeline tracing charts: [https://2381.beast.tracing.cppalliance.org/index.html](https://2381.beast.tracing.cppalliance.org/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-06-07 01:31:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1148097559  

An automated preview of the documentation is available at [https://2381.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2381.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-06-08 22:34:43 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150479825  

The Examples page in the docs needs to be updated:  
https://2381.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/beast/examples.html#beast.examples.clients

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-06-08 22:35:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150480418  

We still need to preserve the file "http_client_sync.cpp" (this pull request renames it to "http_client_sync_get.cpp"). Otherwise anyone whose web page or email links to the file will break.

---

## Comment 10

> Username: sehe  
> Created_at: 2022-06-08 23:13:05 UTC  
> Updated_at: 2022-06-08 23:17:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150504666  

> The Examples page in the docs needs to be updated:  
https://2381.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/beast/examples.html#beast.examples.clients  
  
I tried to sollicit opinion on how to better organize the examples instead (I mentioned it as the reason why I did not add the other examples).  
  
My current best plan is to introduce another defaulted CLI option (ugh) like "get", "post-formdata", "post-json" or "post-multipart". Alternative names: "get", "form", "json", "upload".  
  
I guess the CLI interface - as a proper interface - is horrible, but the point is not to provide real-life CLI interface. What matters is that we can show several kinds of requests in the space of one example, highlighting the minimal set of differences, instead of duplicating the entire "bulky" example, leaving it to visitors to figure out the difference.  
  
> We still need to preserve the file "http_client_sync.cpp" (this pull request renames it to "http_client_sync_get.cpp"). Otherwise anyone whose web page or email links to the file will break.  
  
Keeping in mind the wish to have permanent links, I suggest to keep "get" it's own example, as currently, and just provide a _post flavoured example containing the other three just listed.

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-06-09 00:04:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150530740  

Yeah if you want to reorganize the examples to preserve the existing GET and filename, and then add a larger single example that demonstrates various types of client requests using different command line options, that sounds good.

---

## Comment 12

> Username: sehe  
> Created_at: 2022-06-09 01:50:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150584431  

> Yeah if you want to reorganize the examples to preserve the existing GET and filename, and then add a larger single example that demonstrates various types of client requests using different command line options, that sounds good.  
  
Did that. Adding one or two questions in the code

---

## Comment 13

> Username: sehe  
> Created_at: 2022-06-09 02:19:50 UTC  
> Updated_at: 2022-06-09 02:19:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150598250  

- [ ] add examples to documentation  
- [ ] port to other example flavours? (I'm thinking just async? Not much use in also showing ssl or coro I think)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-06-09 02:21:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1150598910  

![pullrequest](https://2381.beast.tracing.cppalliance.org/pullrequest-a9dfd31b.png)  
Timeline tracing charts: [https://2381.beast.tracing.cppalliance.org/index.html](https://2381.beast.tracing.cppalliance.org/index.html)

---

## Comment 15

> Username: sehe  
> Created_at: 2022-06-16 00:23:57 UTC  
> Updated_at: 2022-06-27 00:00:36 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1157097794  

Went ahead and added a json_body example with an example program that exercises it against `httpbin.org/post`  
  
       example/json-body/http_client_json_body.cpp  
       example/json-body/json_body.hpp  
  
TODO:  
  
 - [x] parse_options  
 - [x] non-sized body writing using chunked encoding  
 - [ ] link documentation  
  
Note also the existing tasks in https://github.com/boostorg/beast/pull/2381#issuecomment-1150598250  
  
@vinniefalco Perhaps it is more consistent to leave the entire http/client/*/ example tree as-is and put all the new examples of this PR under `example/advanced/client/`.

---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-16 00:26:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-1008351864  

📁 example/json-body/json_body.hpp

```diff
  51 |+     */
  52 |+     static std::uint64_t
  53 |+     size(value_type const& v)
```

> Username: vinniefalco  
> Created_at: 2022-06-16 00:26:06 UTC  
> Updated_at: 2022-06-16 00:26:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898583401  

oh no... we don't want to have the fixed size. JSON bodies should always use chunked. Otherwise they can just serialize once to `std::string` and use that instead :)

> Username: sehe  
> Created_at: 2022-06-16 00:31:34 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898585507  

That clears that up. Thanks for the swift answer :) It does mean that it's HTTP/1.1 only. Do we have error codes to diagnose a mismatch already? Neither `bad_version` nor `bad_transfer_encoding` fit the bill, IMO

> Username: sehe  
> Created_at: 2022-06-26 14:23:19 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906826312  

Deleting the `size` member was enough to make things transparently use chunked_encoding. This part of the BodyWriter concept is actually working out quite nicely, there.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-16 00:27:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-1008352779  

📁 example/json-body/json_body.hpp

```diff
 106 |+         {
 107 |+             size_t n = 0;
 108 |+             static_assert(beast::is_const_buffer_sequence<ConstBufferSequence>::value, "Whoops");
```

> Username: vinniefalco  
> Created_at: 2022-06-16 00:27:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898584251  

`BOOST_STATIC_ASSERT`

> Username: sehe  
> Created_at: 2022-06-16 00:33:57 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898586305  

Actually, the static assert was a debug remnant (I'd never use "Whoops"...). I had accidentally used `buffers_begin` instead of `buffer_sequence_begin` and was scratching my head why the element type wasn't a buffer :)


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-16 00:28:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-1008353044  

📁 example/json-body/json_body.hpp

```diff
 108 |+             static_assert(beast::is_const_buffer_sequence<ConstBufferSequence>::value, "Whoops");
 109 |+             for(auto bb = net::buffer_sequence_begin(buffers),
 110 |+                      be = net::buffer_sequence_end(buffers);
```

> Username: vinniefalco  
> Created_at: 2022-06-16 00:28:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898584447  

Try `buffers_range`: https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__buffers_range.html

> Username: sehe  
> Created_at: 2022-06-16 00:35:10 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898586713  

I sort-a-kinda remembered that, but then I also heard an echo of you saying Beast has too many buffer approaches, and I reckoned I could manage with just the Asio facilities here. Any strong opinion why _should_ be `buffers_range` still?


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-16 00:28:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-1008353246  

📁 example/json-body/json_body.hpp

```diff
 112 |+                 ++bb)
 113 |+             {
 114 |+                 n += parser_->write_some(
```

> Username: vinniefalco  
> Created_at: 2022-06-16 00:28:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898584593  

should this be `write` instead?

> Username: sehe  
> Created_at: 2022-06-16 00:36:09 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898587049  

I have to find out. I was of two minds, actually I'm just pushing this draft so I can go to bed :) I'll check this next time I visit this task

> Username: sehe  
> Created_at: 2022-06-26 14:27:31 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906827014  

Yes since we're not going to bother with multi-JSON body (JSONL or similar) we don't need `write_some` to anticipate extra data


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-06-16 00:29:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-1008353445  

📁 example/json-body/json_body.hpp

```diff
 140 |+ 
 141 |+         boost::json::serializer ser_;
 142 |+         char buf_[128];
```

> Username: vinniefalco  
> Created_at: 2022-06-16 00:29:22 UTC  
> Updated_at: 2022-06-16 00:29:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898584724  

whoa.. 128 is way too small! at least 4kb please. It has to be at least the size of a TCP/IP packet but even that is very small because socket I/O overhead will be a larger percentage of the total resource usage.

> Username: sehe  
> Created_at: 2022-06-16 00:37:18 UTC  
> Updated_at: 2022-06-27 00:04:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r898587445  

Another dev/testing value (I've used way smaller too). Again a draft symptom.

> Username: sehe  
> Created_at: 2022-06-26 14:37:51 UTC  
> Updated_at: 2022-06-27 00:04:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906828460  

I couldn't leave well enough alone and actually tested this, because I was wondering how big the penalty would be in the absense of any real IO (which would obviously suffer from small buffer size):  
  
```c++  
#define NONIUS_RUNNER  
#include <nonius/benchmark.h++>  
#include <nonius/main.h++>  
  
NONIUS_BENCHMARK("naive", [](nonius::chronometer cm) {  
    cm.measure([] { return json::serialize(json_value).length(); });  
})  
  
NONIUS_BENCHMARK("stream_128b", [](nonius::chronometer cm) {  
    cm.measure([] { return stream_size<128>(json_value); });  
})  
  
NONIUS_BENCHMARK("stream_4k", [](nonius::chronometer cm) {  
    cm.measure([] { return stream_size<4096>(json_value); });  
})  
```  
  
Output:   
![image](https://user-images.githubusercontent.com/324097/175819476-0b74e00d-f68b-4d66-8833-7646aee09945.png)  
  
So yeah, using 128 byte buffers is literally as expensive as just serializing to a string (iff it fits in memory anyways and with slighlty lower variance). I'm actually a bit suprised. I thought I'd have to craft a json with especially large lexemes to get this behaviour. Lesson learned.

> Username: sehe  
> Created_at: 2022-06-26 14:38:22 UTC  
> Updated_at: 2022-06-27 00:04:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906828573  

Oh full interactive chart (including summary) http://stackoverflow-sehe.s3.amazonaws.com/dbf830c4-1849-42cd-9ed3-ef11af9162ba/sample.html input was my Google location history of 612MiB


---

## Comment 21

> Username: vinniefalco  
> Created_at: 2022-06-16 00:31:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1157101185  

I'm not particularly concerned about "multi-root JSON bodies" as that is not standard JSON. I assume you mean more than one JSON in a body? There's no spec for that, its outside the scope of JSON.

---

## Comment 22

> Username: vinniefalco  
> Created_at: 2022-06-16 00:32:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1157101479  

`parse_options` are going to be a problem because Beast does not provide a clean way to customize the body read or write operation. We should choose sensible defaults and call it a day.

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2022-06-16 00:33:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1157101967  

> reference value_type?  
  
No idea what this is  
  
> non-sized body writing using chunked encoding  
  
All JSON bodies should use chunked, because if you are going to measure the serialized size then you might as well just use a string body.

---

## Comment 24

> Username: sehe  
> Created_at: 2022-06-16 00:39:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1157104670  

> > reference value_type?  
>   
> No idea what this is  
  
Basically `std::reference_wrapper<json::value /*const*/>` instead of copying the value just in order to serialize it. That seems... dumb. (At least on the receiving side there's always the option to `std::move(res.body())`)

---

## Comment 25

> Username: sehe  
> Created_at: 2022-06-16 00:42:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1157106200  

> > non-sized body writing using chunked encoding  
>   
> All JSON bodies should use chunked, because if you are going to measure the serialized size then you might as well just use a string body.  
  
That was actually my option B. Somehow I failed to reach the realization that it's not even worth implementing the non-chunked version as it quite literally is `message<..., string_body> msg(verb, target, 11, serialize(jv))` indeed. That should have clued me in to start out with option B :derp:

---

## Review 26 [Commented]

> Username: sehe  
> Created_at: 2022-06-27 00:04:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-997372076  

📁 example/http/client/sync/http_client_sync_json.cpp

```diff
  68 |+         req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);
  69 |+ 
  70 |+         std::string payload = "{\"m_list\":[1,2,3]}";
```

> Username: sehe  
> Created_at: 2022-06-07 00:21:28 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890663242  

suggest  
```c++  
boost::json::value const jv = {"m_list", {1, 2, 3}};  
req.set(http::field::content_type, "application/json");  
req.body() = serialize(jv);  
req.prepare_payload();  
```  
  
So it is more instructive than just another hardcoded string body

---

📁 example/http/client/sync/http_client_sync_json.cpp

```diff
  70 |+         std::string payload = "{\"m_list\":[1,2,3]}";
  71 |+         req.set(http::field::content_type, "application/json");
  72 |+         req.set(http::field::content_length, payload.size());
```

> Username: sehe  
> Created_at: 2022-06-07 00:26:28 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890665175  

Use `prepare_payload()` instead

---

📁 example/http/client/sync/http_client_sync_json.cpp

```diff
  30 |+ using tcp = net::ip::tcp;           // from <boost/asio/ip/tcp.hpp>
  31 |+ 
  32 |+ // Performs an HTTP JSON and prints the response
```

> Username: sehe  
> Created_at: 2022-06-07 00:26:50 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890665344  

There's really no such thing


📁 example/http/client/sync/http_client_sync_post.cpp

```diff
  70 |+         std::string payload = "var1=1&var2=2";
  71 |+         req.set(http::field::content_type, "application/x-www-form-urlencoded");
  72 |+         req.set(http::field::content_length, payload.size());
```

> Username: sehe  
> Created_at: 2022-06-07 00:27:12 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890665472  

Use `prepare_payload()` instead

---

📁 example/http/client/sync/http_client_sync_post.cpp

```diff
  50 |+         "80",
  51 |+         "/",
  52 |+         "formdata",
```

> Username: sehe  
> Created_at: 2022-06-09 01:56:47 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r893003315  

This makes the defaults so much more natural. Perhaps something we could use elsewhere.  
  
In the other examples I am not sure the addition of a class for argument parsing is worth it though. Just saying

---

📁 example/http/client/sync/http_client_sync_post.cpp

```diff
  36 |+ struct parameters {
  37 |+     explicit parameters(int argc, char** argv);
  38 |+     http::message_generator make_request() const;
```

> Username: sehe  
> Created_at: 2022-06-09 01:58:07 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r893003882  

For this example we could easily just return `message<string_body>`.   
  
This choice is to make beginners more likely to fail "up" when they do need other concrete message types.

---

📁 example/http/client/sync/http_client_sync_post.cpp

```diff
 162 |+ 
 163 |+         // Send the HTTP request to the remote host
 164 |+         beast::write(stream, req);
```

> Username: sehe  
> Created_at: 2022-06-09 02:00:19 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r893004639  

I dislike that we need to change `http::write` to `beast::write` to write `http::message_generator`.  
  
I mean, it technically makes sense, but it's not intuitive as a user.  
  
Is anyone else bothered, can we "solve" it for the user?

> Username: vinniefalco  
> Created_at: 2022-07-19 19:23:45 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r924880373  

its a little weird, yes

---

📁 example/http/client/sync/http_client_sync_post.cpp

```diff
  97 |+     } else if(arg == "json")
  98 |+     {
  99 |+         payload << boost::json::value{"m_list", {1, 2, 3}};
```

> Username: sehe  
> Created_at: 2022-06-09 02:10:45 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r893008281  

Do we know of nice example payloads? More imaginative... Something many users will recognize?  
  
I'm a scrub, so this is actually fine for me.


📁 example/http/client/sync/http_client_sync_file_upload.cpp

```diff
  77 |+ 
  78 |+         std::string payload = "--AaB03x\n" // This is the boundary to limit the start/end of a part. It may be any string. More info on the RFC 2388 (https://datatracker.ietf.org/doc/html/rfc2388)
  79 |+                               "Content-Disposition: form-data; name=\"comment\"\n"
```

> Username: sehe  
> Created_at: 2022-06-07 00:27:45 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890665665  

I think multipart MIME requires CRLF

---

📁 example/http/client/sync/http_client_sync_file_upload.cpp

```diff
  81 |+                               "Larry\n" 
  82 |+                               "--AaB03x\n"
  83 |+                               "Content-Disposition: form-data; name=\"files\"; filename=\"" + file.substr(file.find_last_of("/")+1) + "\"\n"
```

> Username: sehe  
> Created_at: 2022-06-07 00:28:45 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890666056  

Suggest to use `filesystem::path` to do filename manipulation.  
  
Suggest to use ostreamable `path` to output quoted filename.

---

📁 example/http/client/sync/http_client_sync_file_upload.cpp

```diff
  84 |+                               "Content-Type: application/octet-stream\n"
  85 |+                               "\n"
  86 |+                               + file_buffer.str() + "\n"
```

> Username: sehe  
> Created_at: 2022-06-07 00:28:56 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890666128  

Suggest to read into string directly

---

📁 example/http/client/sync/http_client_sync_file_upload.cpp

```diff
  74 |+         std::ifstream f(file);
  75 |+         std::stringstream file_buffer;
  76 |+         file_buffer << f.rdbuf(); // It may be binary files.
```

> Username: sehe  
> Created_at: 2022-06-07 00:29:17 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890666248  

If so, add `std::ios::binary` on open.


📁 example/http/client/sync/CMakeLists.txt

```diff
  42 | 
  43 | target_link_libraries(http-client-sync-post-json
  44 |+     boost_json
```

> Username: sehe  
> Created_at: 2022-06-07 01:23:46 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r890685778  

@vinniefalco this feels wrong. I couldn't make it work using Boost::json like I expected would work. Should that have worked?

> Username: sehe  
> Created_at: 2022-06-08 23:37:14 UTC  
> Updated_at: 2022-06-27 00:04:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r892949007  

@pdimov Does this linker input seem okay to you?

> Username: pdimov  
> Created_at: 2022-06-27 00:06:42 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906890909  

Boost::json should work.

> Username: sehe  
> Created_at: 2022-06-27 00:10:34 UTC  
> Updated_at: 2022-06-27 00:10:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906891500  

@pdimov I doesn't for me:  
  
```  
|| CMake Error at example/json-body/CMakeLists.txt:19 (target_link_libraries):  
||   Target "http-client-json-body" links to:  
||   
||     Boost::json  
||   
||   but the target was not found.  Possible reasons include:  
||   
||     * There is a typo in the target name.  
||     * A find_package call is missing for an IMPORTED target.  
||     * An ALIAS target is missing.  
```

> Username: sehe  
> Created_at: 2022-06-27 00:15:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906892305  

@pdimov Seems I can force the issue by slipping in `json` in the required components in a seemingly random place in the Beast global CMakeLists.txt: https://github.com/boostorg/beast/blob/develop/CMakeLists.txt#L192  
  
However, we may not want that because Boost JSON should not become a Beast dependency. In fact, that's exactly why `json_body` is only gonna be an example.

> Username: pdimov  
> Created_at: 2022-06-27 00:17:07 UTC  
> Updated_at: 2022-06-27 00:17:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906892583  

Yeah, I was just going to suggest that. The place must not be so random.

> Username: sehe  
> Created_at: 2022-06-27 00:19:26 UTC  
> Updated_at: 2022-06-27 00:19:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906892942  

I don't understand why it is in a conditional section only "unless MSVC is involved"(?). That's why I'd normally never stuff it in there. But it was the only `find_package(Boost` in sight, so I tried it. Do you reckon we can get away with that or should we just keep it as is due to dependency management?

> Username: pdimov  
> Created_at: 2022-06-27 00:20:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906893127  

Linking to `boost_json` just adds `-lboost_json` to the link command. Whether the library will then be found is anyone's guess. A "normal" project needs to find Boost.JSON in order to link to it.  
  
I suppose you can add the `find_package(Boost REQUIRED COMPONENTS json)` to the `json-body` CML file, but I'm not sure whether FindBoost supports multiple calls like that.

> Username: sehe  
> Created_at: 2022-06-27 00:26:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r906894042  

That appears to work, I see that the sub-directory reports the superset of required components, so at least something here seems to "interfere fortunately" - I'm assuming that might just mean it works. We'll see whether CI barfs on it I guess when I push it tomorrow to test.


---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2022-06-27 10:36:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1167184787  

![pullrequest](https://2381.beast.tracing.cppalliance.org/pullrequest-b3b7d543.png)  
Timeline tracing charts: [https://2381.beast.tracing.cppalliance.org/index.html](https://2381.beast.tracing.cppalliance.org/index.html)

---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-19 19:25:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2381#pullrequestreview-1043970387  

📁 example/http/client/sync/http_client_sync_post.cpp

```diff
  45 |+     int version() const { return !strcmp("1.0", args_.at(5)) ? 10 : 11; }
  46 |+ 
  47 |+     std::array<char const*, 6> args_{{
```

> Username: vinniefalco  
> Created_at: 2022-07-19 19:25:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r924881578  

What is all this `parameters`? I don't like introducing a bunch of new idioms..

> Username: sehe  
> Created_at: 2022-07-19 21:25:46 UTC  
> Updated_at: 2022-07-19 21:25:47 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r924970225  

Yeah. I don't like very intransparent spaghetti around arvg[3] and argv[argc>3? 5 : 2]. Would you like to introduce Boost Program Options instead :)  
  
Personally, I dislike that the cookbook-style examples take command line arguments at all. It just distracts from what is being demonstrated. At least with the parameters helper you can ignore the cruft when you read the program.  
  
For servers, perhaps a docroot, maybe. Though current working directory would be fine for me.

> Username: vinniefalco  
> Created_at: 2022-07-19 21:43:19 UTC  
> Updated_at: 2022-07-19 21:43:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r924981341  

I like the examples the way they are written, which introduce as few new idioms as possible. There is no confusion or things to learn here, because it is straight C++ with no user defined types:  
https://github.com/boostorg/beast/blob/00293a6adb5383fe14217a8916dd7ff79a857483/example/advanced/server/advanced_server.cpp#L612

> Username: sehe  
> Created_at: 2022-07-19 21:52:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#discussion_r924986465  

Yeah I got that, but to me the "it's all ints and char, what's the big deal" is a meager excuse. It would be 10x more readable with just hardcoded defaults. If that doesn't suit the user, they can choose to edit or indeed take from `argv` (since it is so trivial).  
  
I really do not wish to complicate that command line, just putting more blocks on top of the jenga pile, so I'll mull on a better way to solve this again.


---

## Comment 29

> Username: klemens-morgenstern  
> Created_at: 2022-09-24 04:32:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1256853761  

Shoild this be combined with #2511 ?

---

## Comment 30

> Username: ashtum  
> Created_at: 2023-12-27 15:40:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2381#issuecomment-1870417644  

Closing this pull request as:  
- The `json_body` example has already been added in https://github.com/boostorg/beast/pull/2511.  
- The `http_client_sync_post` example is too complex to be considered a suitable example.

---
