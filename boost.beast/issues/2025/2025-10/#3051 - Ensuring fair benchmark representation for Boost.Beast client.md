# #3051 - Ensuring fair benchmark representation for Boost.Beast client [Closed]

> Username: InfiniteConsult  
> Created at: 2025-10-26 21:48:03 UTC  
> Updated at: 2025-10-30 03:31:24 UTC  
> Closed at: 2025-10-30 03:31:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3051  

Hi Boost.Beast maintainers,  
  
I've been working on a project comparing HTTP client performance across C, C++, Rust, and Python, implemented from first principles:  
https://github.com/InfiniteConsult/0004_std_lib_http_client  
  
For this comparison, I've included Boost.Beast as a C++ baseline, aiming for an idiomatic and reasonably optimized implementation based on the documentation. The specific benchmark client harness code is here:  
https://github.com/InfiniteConsult/0004_std_lib_http_client/blob/main/benchmark/clients/cpp/boost_client.cpp  
  
The primary scenario involves high-throughput POST requests (varying sizes) over a single keep-alive connection. The current implementation (particularly the `--unsafe` mode using `span_body`) performs very well, as expected.  
  
My goal is to ensure a fair representation of Boost.Beast's capabilities in this specific context. Before finalizing my analysis, I wanted to quickly ask: are there any obvious misuses of the API or significantly more idiomatic approaches for this type of high-throughput POST loop that I might have missed in the harness code linked above?  
  
Any brief feedback on ensuring a fair comparison would be greatly appreciated. Thanks for building such a great library!  
  
Best regards,  
  
Warren Jitsing

---

## Comment 1

> Username: sehe  
> Created at: 2025-10-27 00:54:13 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449114943  

Doing  
  
```  
./build/benchmark/data_generator --num-requests 100000 --max-length 102400  
./build/benchmark/benchmark_server --num-responses 100000&  
time ./build/benchmark/boost_client --host localhost --port 8080 --num-requests 10000  
```  
  
I got  
  
```  
boost_client: completed 10000 requests.  
  
real    0m1.655s  
user    0m0.672s  
sys     0m0.237s  
```  
  
> *side notes* _(note that it's important that the server is fully initialized; also there is no proper diagnostics when the server handles fewer responses than the number of requests)_  
  
**Just** adding `set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native")` to the main CMakeLists.txt consistently lowers the timing by ~300ms  
  
```  
boost_client: completed 10000 requests.  
  
real    0m1.333s  
user    0m0.448s  
sys     0m0.258s  
```  
  
That's almost 20% of the runtime shaved by just adding an obvious compiler flag. Those timings are similar under both GCC 14.3.0 or GCC 15.2.0. Using Clang 20.1.6 ran into a libstdc++.  
  
Given this quick win I'm pretty certain there will be bigger performance gains to be had. Can you explain what makes using string_views "unsafe"? When creating a benchmark, this is 100% what I'd expect to happen. After all, one wants to benchmark the IO speed, not copying memory?

---

## Comment 2

> Username: sehe  
> Created at: 2025-10-27 02:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449291766  

- any reason why the server reads RAW data instead of using `http::read`? E.g. chunked encoding will ruin your day now. Http is not just plain text over sockets  
 - any reason why the "unsafe" response path sets the body() which is never written?  
 - any reason why the checksum never spans more than 8 bits? Interestingly, when expanding it to use the full 64 bits makes the benchmark >350% slower  
  
    ```diff  
    diff --git a/benchmark/clients/cpp/boost_client.cpp b/benchmark/clients/cpp/boost_client.cpp  
    index 6ed18e6..6b734d5 100644  
    --- a/benchmark/clients/cpp/boost_client.cpp  
    +++ b/benchmark/clients/cpp/boost_client.cpp  
    @@ -95,8 +95,9 @@ bool read_benchmark_data(std::string const& filename, BenchmarkData& data) {  
     }  
       
     uint64_t xor_checksum(std::string_view data) {  
    -    return std::accumulate(data.begin(), data.end(), std::uint64_t{0},  
    -                           [](uint64_t acc, char c) { return acc ^ static_cast<unsigned char>(c); });  
    +    return std::accumulate(data.begin(), data.end(), std::uint64_t{0}, [](uint64_t acc, char c) {  
    +        return std::rotr(acc, 7) ^ static_cast<unsigned char>(c);  
    +    });  
     }  
       
     uint64_t get_nanoseconds() {  
    diff --git a/benchmark/server/main.cpp b/benchmark/server/main.cpp  
    index d965843..7dfb109 100644  
    --- a/benchmark/server/main.cpp  
    +++ b/benchmark/server/main.cpp  
    @@ -75,7 +75,7 @@ uint64_t xor_checksum(beast::string_view body) {  
         uint64_t             sum  = 0;  
         unsigned char const* data = reinterpret_cast<unsigned char const*>(body.data());  
         for (size_t i = 0; i < body.size(); ++i) {  
    -        sum ^= data[i];  
    +        sum = std::rotr(sum, 7) ^ data[i];  
         }  
         return sum;  
     }  
    ```  
 - it follows that only `--no-verify` benchmarks in release mode seem relevant  
 - any reason not to use async interfaces?  
 - any reason not to use composed operations (e.g. instead of reading body fragments in a loop with `read_some`, you can use `asio::read` with `read_exactly(body_len)` and just sit back for the result or error code.

---

## Comment 3

> Username: InfiniteConsult  
> Created at: 2025-10-27 02:46:59 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449296415  

Hi Sehe,  
  
Thank you so much. I can't believe I forgot such an obvious flag.  
  
Verification of number of responses is either done when running with leaving out `--no-verify` on the client or setting `--verify true` on the server. I ran these with the checksum matching before I ran the benchmarks. The server only really shuts down with the correct number of responses also. I think your timings had verify on in the client, judging from the CPU usage. I will see how it goes with -march=native with verify off. I will adjust the inequality in the analyze_latencies script to be strictly greater than zero so we can more easily see if we had missing values.  
  
The "safe"/"unsafe" distinction is just maybe some poorly worded way of hinting at ownership. The repo is just supposed to be a free pedagogical article for a basic, gentle introduction to network programming/systems programming in the different languages. "safe" in the hand coded client's case means that the response object will be valid after the next request the user makes (so owns a copy of the data) while unsafe means the response object will be invalidated in the next request (points to a shared buffer, zero-copy in user space [still has kernel buffer copy]). So clients will need to consume the full response in the unsafe version before making the next request. I am not sure if this is the case in the boost version of the benchmark for string_view and string.  
  
The different benchmarks e.g. testing copy vs no copy or the effect of vectored IO in the C version, is just to make the theory in the article more tangible to the readers. So they can see, say, in the large throughput case or uplink heavy case, that the single system call using writev can save some time vs the alternatives of serializing all the data into a single buffer or perhaps making two write() calls (which I probably should have added a toggle for).  
  
Thank you for all your guidance above. I really appreciate the help

---

## Comment 4

> Username: sehe  
> Created at: 2025-10-27 02:55:53 UTC  
> Updated at: 2025-10-27 02:56:05 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449309588  

> Verification of number of responses is either done when running with leaving out --no-verify on the client or setting --verify true on the server  
  
These are the defaults  
  
> The server only really shuts down with the correct number of responses also.  
  
This is not my experience.  See my initial comment where I give the server numrequests as 100'000 vs. the client 10'000. The server always exits when the client disconnects. There is no error detected. Similarly, if the server serves fewer responses, the client succeeded (although an "End of stream" message would seen on the console).  
  
> I think your timings had verify on in the client  
  
Yes, this is the default. I showed you my command lines.  
  
I understand your explanation about `unsafe`. I really think the word choice is unfortunate, since given your code the string views are perfectly fine.

---

## Comment 5

> Username: InfiniteConsult  
> Created at: 2025-10-27 02:56:19 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449310119  

Hi Sehe  
  
"any reason why the server reads RAW data instead of using http::read? E.g. chunked encoding will ruin your day now. Http is not just plain text over sockets" . I didn't support chunked encoding in the client. I just wanted a basic example for like juniors to work through and just learn a bit. Yes, I probably should have worded it as HTTP1/1 headers are plain text.  
  
"any reason why the "unsafe" response path sets the body() which is never written?" - I will check this out. Thank you! Probably a major error.  
  
"any reason why the checksum never spans more than 8 bits? Interestingly, when expanding it to use the full 64 bits makes the benchmark >350% slower", I started with xxh3 and then just moved to a simpler checksum and forgot to truncate. But thank you so much.  
  
"it follows that only --no-verify benchmarks in release mode seem relevant" - yes, this is true for comparing pure speed. Most of the benchmarks are just to illustrate to a beginner the effects of different memory models (copy/no-copy) or io models (write vs writev).  
  
"any reason not to use async interfaces?" - just to keep the focus for this article as simple as possible. I will hopefully expand into epoll and async interfaces in a future article but I didn't want there to be too much content (it's already a bit large) in a single introductory article across the four languages.  
  
"any reason not to use composed operations (e.g. instead of reading body fragments in a loop with read_some, you can use asio::read with read_exactly(body_len) and just sit back for the result or error code." - Thank you! I will try to implement this  
  
Thank you for all the additional points!

---

## Comment 6

> Username: InfiniteConsult  
> Created at: 2025-10-27 02:58:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449312949  

> > Verification of number of responses is either done when running with leaving out --no-verify on the client or setting --verify true on the server  
>   
> These are the defaults  
>   
> > The server only really shuts down with the correct number of responses also.  
>   
> This is not my experience. See my initial comment where I give the server numrequests as 100'000 vs. the client 10'000. The server always exits when the client disconnects. There is no error detected. Similarly, if the server serves fewer responses, the client succeeded (although an "End of stream" message would seen on the console).  
>   
> > I think your timings had verify on in the client  
>   
> Yes, this is the default. I showed you my command lines.  
>   
> I understand your explanation about `unsafe`. I really think the word choice is unfortunate, since given your code the string views are perfectly fine.  
  
Thanks Sehe. I will add extra validation for the server. Yeah, the word choice is a bit unfortunate. I can probably change it. This is just a work in progress right now. Thank you so much!

---

## Comment 7

> Username: sehe  
> Created at: 2025-10-27 04:37:20 UTC  
> Updated at: 2025-10-27 04:37:47 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449483352  

> I didn't support chunked encoding in the client. I just wanted a basic example for like juniors to work through and just learn a bit.  
  
Learning how to use Beast implies **using** the library. Instead, doing raw `read_some` on the socket behind Beast's back is the opposite. Also, using `buffer_body` is with repeated `http::read` is hardly more complicated and solves the entire conundrum (you don't even **see** the chunking being parsed by the parser object. See for example: https://stackoverflow.com/questions/75803164/boostbeast-http-chunked-response-buffer/75807592#75807592)

---

## Comment 8

> Username: InfiniteConsult  
> Created at: 2025-10-27 05:14:39 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3449554256  

Hi Sehe,  
  
I mean, in the from scratch clients I implemented in C/C++/Rust/Python, I hadn't implemented chunked encoding. https://github.com/InfiniteConsult/0004_std_lib_http_client/tree/main/src  
  
I will check out the stack overflow post but I would like to keep the comparison using content-length, ceteris paribus, since that is what is implemented in this basic educational library and what I used with libcurl/requests/reqwest in the C/Python/Rust baselines. Thanks for the great usage example.  
  
I will implement the points you made in this thread a bit later today. Please remember, the repo is just supposed to be purely educational and is never meant to be a production library. I wrote most of the code in a few weeks of my spare time. I arrived at the current configuration in the boost client after an initial attempt to optimize. I am not too familiar with boost::beast::http (I mostly used boost::beast::websocket a lot in the past). The current configuration already puts boost as the top tier performing library in the benchmarks results.  
  
Thanks for helping me refine the usage of boost in the project! I really appreciate the help and guidance. I will let you know after your points have been implemented.

---

## Comment 9

> Username: InfiniteConsult  
> Created at: 2025-10-27 08:42:32 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3450086996  

Hi Sehe, I tried a few different approaches. A few days ago I tried the multi-buffer approach but I didn't seem to get a contiguous memory read. I am just resetting to the read_some loop because boost is still highly performant with it and ranks at the top of the benchmarks. I spent a few hours this morning trying to fix it. I did, however, implement march=native across C/C++/Rust. For this simple, educational benchmark, it is probably fine but maybe you can see what I am doing wrong with the idiomatic approaches. Again, this is probably due to me not understanding something about the library. Thank you for your help and guidance from before. Below are rough outlines of my attempts for idiomatic boost usage  
  
  
## 1\. Using `asio::read` with `transfer_exactly`  
  
This approach used `http::read_header` first and then attempted to read the exact body size using a low-level Asio function. It resulted in the client stalling indefinitely on keep-alive connections.  
  
```cpp  
        // --- Stalled Attempt using asio::read ---  
        http::response_parser<http::empty_body> parser;  
        parser.skip(true);  
        http::read_header(stream, buffer, parser, ec);  
        if(ec) { /* ... error handling ... */ break; }  
  
        if (parser.content_length()) {  
            size_t body_size = *parser.content_length();  
            buffer.reserve(body_size); // Optional optimization  
            auto mutable_buffer = buffer.prepare(body_size); // Get buffer space  
  
            // *** THE PROBLEMATIC CALL ***  
            // This blocks indefinitely on keep-alive connections  
            size_t bytes_read = asio::read(stream, mutable_buffer, asio::transfer_exactly(body_size), ec);  
  
            buffer.commit(bytes_read); // Commit data (if read ever finished)  
  
            // ... (Error handling and checks that were never reached due to stall) ...  
        }  
        auto client_receive_time = get_nanoseconds();  
        std::string_view body(static_cast<const char*>(buffer.data().data()), buffer.size());  
        // ... (Rest of processing) ...  
```  
  
-----  
  
## 2\. Using `http::read` with `dynamic_body`  
  
This approach used the more idiomatic Beast function `http::read` intended to handle the full message reading. However, in various configurations (parser inside/outside loop, with/without `eager(false)`), it consistently resulted in `http::read` reporting success (`ec` was false) but leaving the `buffer` empty (`buffer.size() == 0`) on subsequent requests in the keep-alive loop, leading to "Response body too short" warnings.  
  
Here's the version with the parser created inside the loop (which still failed):  
  
```cpp  
        // --- Failed Attempt using http::read + dynamic_body ---  
        buffer.clear(); // Clear the external buffer  
  
        // Create parser inside the loop for clean state  
        http::response_parser<http::dynamic_body> parser;  
  
        // Read the entire HTTP response (headers and body) into 'buffer'  
        http::read(stream, buffer, parser, ec); // <-- PROBLEM: Leaves buffer empty after first request  
  
        // DEBUG CHECK showed: ec == Success, but buffer.size() == 0  
        // std::cerr << ">> DEBUG: http::read completed..." << std::endl;  
  
        // Handle read errors  
        size_t expected_body_size = 0;  
        bool content_length_present = parser.get().count(http::field::content_length);  
        if (content_length_present) { /* ... get expected_body_size ... */ }  
  
        // Error checking for ec...  
        if(ec) { /* ... handle errors ... */ continue; }  
  
        // *** THIS CHECK WOULD FAIL ***  
        if (content_length_present && buffer.size() < expected_body_size) {  
             std::cerr << "Warning: http::read finished without error but buffer size (" << buffer.size()  
                       << ") is less than Content-Length (" << expected_body_size << "). Request " << i << std::endl;  
        }  
  
        auto client_receive_time = get_nanoseconds();  
        std::string_view body(static_cast<const char*>(buffer.data().data()), buffer.size()); // <-- body view has size 0  
  
        // --- Latency & Verification Logic (Fails due to empty body) ---  
        // ... (Checks for body.length() < 35 or body.length() < 19 trigger warnings) ...  
```  
  
  
  
## 3\. `buffer_body` streaming example but still needed to accumulate the body, resulting in extra copies and the use of `goto`.  
  
```cpp  
template<class Stream>  
void run_benchmark(Stream& stream, const Config& config, const BenchmarkData& data, std::vector<int64_t>& latencies) {  
    std::string payload_buffer; // Used only for verify=true path  
    beast::error_code ec;  
    // Buffer for beast internal use during reads  
    beast::flat_buffer buffer;  
    // Parser using buffer_body for incremental body processing  
    http::response_parser<http::buffer_body> parser;  
    // Convenience reference to the body member within the parser's message  
    auto& body_ref = parser.get().body();  
    // Temporary storage for body chunks read by buffer_body  
    std::array<char, 4096> chunk_buffer; // Using std::array as a fixed-size buffer  
    // Vector to accumulate the full body if needed (for timestamp/verification)  
    std::vector<char> full_body_accumulator; // <-- Accumulator adds a copy step  
    bool request_failed_in_loop = false; // Flag to skip processing if read loop fails - Added to replace goto  
  
    for (uint64_t i = 0; i < config.num_requests; ++i) {  
        size_t req_size = data.sizes[i % data.sizes.size()];  
        std::string_view body_slice(data.data_block.data(), req_size);  
        request_failed_in_loop = false; // Reset flag  
  
        // --- Request Sending Logic (Unchanged) ---  
        // ... (if config.verify) ...  
        // ... (else if config.unsafe_res) ...  
        // ... (else) ...  
        if(ec) { std::cerr << "Write failed: " << ec.message() << std::endl; break; }  
  
        // --- *** RESPONSE READING LOGIC using http::buffer_body + Accumulator *** ---  
        parser = {}; // Re-initialize parser state  
        buffer.clear();  
        full_body_accumulator.clear();  
        full_body_accumulator.reserve(1024*1024 + 64); // Pre-allocate  
  
        // 1. Read header  
        http::read_header(stream, buffer, parser, ec);  
        if(ec) { std::cerr << "Read header failed: " << ec.message() << std::endl; break; }  
  
        // 2. Loop to read body chunks  
        while (!parser.is_done()) {  
            body_ref.data = chunk_buffer.data();  
            body_ref.size = chunk_buffer.size();  
  
            http::read(stream, buffer, parser, ec); // Reads into buffer, parses/copies into chunk_buffer  
  
            size_t bytes_in_chunk = chunk_buffer.size() - body_ref.size;  
            if (bytes_in_chunk > 0) {  
                 // *** COPY STEP 2: Chunk -> Accumulator ***  
                 full_body_accumulator.insert(full_body_accumulator.end(), chunk_buffer.data(), chunk_buffer.data() + bytes_in_chunk);  
            }  
  
            // Error Handling within loop  
            if(ec == http::error::need_buffer) {  
                ec = {}; // Reset 'need_buffer' and continue reading  
                continue;  
            } else if (ec == http::error::end_of_stream) {  
                 if (!parser.is_done()) {  
                     std::cerr << "Read body failed: Connection closed prematurely before parser finished. Request " << i << std::endl;  
                     request_failed_in_loop = true;  
                     break; // Exit body read loop  
                 }  
                 break; // EOF after parser done is okay  
            } else if (ec) {  
                std::cerr << "Read body chunk failed: " << ec.message() << ". Request " << i << std::endl;  
                request_failed_in_loop = true;  
                break; // Exit body read loop  
            }  
        } // End while (!parser.is_done())  
  
        if (request_failed_in_loop) {  
            latencies[i] = -1;  
            continue; // Go to next request  
        }  
  
        auto client_receive_time = get_nanoseconds();  
  
        // 3. Create view into the accumulated body  
        std::string_view body(full_body_accumulator.data(), full_body_accumulator.size());  
  
        // --- Latency Calculation & Verification Logic (Unchanged) ---  
        // ... (Checks based on 'body' view) ...  
  
    } // End for loop  
}  
```  
  
This version correctly uses the `buffer_body` mechanics but introduces the extra copy into `full_body_accumulator`, making it less efficient for the specific goal than the working `read_some` loop or the (theoretically correct but problematic in practice) `dynamic_body` approach.

---

## Comment 10

> Username: sehe  
> Created at: 2025-10-28 02:45:42 UTC  
> Updated at: 2025-10-28 02:46:16 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3454299101  

Find here my review and implementation examples:  
  
https://github.com/sehe/0004_std_lib_http_client/commits/main/  
  
----  
  
#1 Composed asio::read  
  
 see https://github.com/sehe/0004_std_lib_http_client/pull/1/files  
  
-----  
  
#2 dynamic_body  
  
I'd in fact simplify all the way to `string_body` as *that's* what you end up making anyways: https://github.com/sehe/0004_std_lib_http_client/pull/2  
  
> commit a0fad92e2c0e79c85105460bc99c680db202f408  
> Author: sehe <sgheeren@gmail.com>  
> Date:   Tue Oct 28 02:20:39 2025 +0100  
>   
>     Using string_body in server  
>       
>      benchmark/server/main.cpp | 97 +++++++++----------------------------------------------------------------------------------------  
>      9 insertions(+), 88 deletions(-)  
>       
>      80 LoC gone, properly supporting things like chunked encoding AND  
>      becomes marginally faster (~5%)  
>   
> commit 00ff5514c51cdeeacd5751c2877a7d7f58cb1372 (HEAD -> string_body, origin/string_body)  
> Author: sehe <sgheeren@gmail.com>  
> Date:   Tue Oct 28 02:37:16 2025 +0100  
>   
>     Using dynamic_body in boost_client  
>       
>      benchmark/clients/cpp/boost_client.cpp | 51 +++++++++++++++++++--------------------------------  
>      19 insertions(+), 32 deletions(-)  
>       
>     Even though the code becomes simpler (and we avoid defragmenting the  
>     reponse body) the overall speed is much slower. This is interesting for  
>     Beast devs, as the server side was not slowed down.  
  
  
-----  
  
#3 Buffer body  
  
Although it is as slow as dynamic_body/string_body for the client, [724fb7ce7136dea67bad65ce6c066888a664f679](https://github.com/sehe/0004_std_lib_http_client/pull/3/commits/724fb7ce7136dea67bad65ce6c066888a664f679) shows how to use a buffer-body. It also shows that you donot *need* to accumulate the full body for the current purposes (you can do the checksum calculations on the fly).  
  
All of the above still pass the verification checks.

---

## Comment 11

> Username: InfiniteConsult  
> Created at: 2025-10-28 04:18:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3051#issuecomment-3454477758  

Thank you so much Sehe! You are amazing! I will add an acknowledgements section and credit you and link to this issue! I really appreciate the help
