# #1734 - Buffer leak in basic_parser::put [Closed]

> Username: inetic  
> Created at: 2019-10-18 08:27:25 UTC  
> Updated at: 2019-10-20 15:33:37 UTC  
> Closed at: 2019-10-20 15:33:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1734  

The version of [`basic_parser::put` that takes a buffer _sequence_ as an argument](https://github.com/boostorg/beast/blob/045dfeb47f6750a940ced9202af0a6331bdcb085/include/boost/beast/http/impl/basic_parser.hpp#L25) has a bug in the [fallback code path](https://github.com/boostorg/beast/blob/045dfeb47f6750a940ced9202af0a6331bdcb085/include/boost/beast/http/impl/basic_parser.hpp#L53-L56) (the one that is not optimized for single buffer sequences nor those whose size is smaller than `max_stack_buffer`).  
  
The issue is that while `buf_.get()` has size `buf_len_`, it has only `size` number of _valid_ octets. This causes some of the invalid octets (those with index >=`size` and <`buf_len_`) to "leak" from previous calls to `put` into the current one.  
  
The [fix](https://github.com/inetic/beast/commit/aff4f4f3f9dc5aa0b7bf85e4b27a2d842c856f62) is simple. I'm attempting to write a unit test, but that's a bit more challenging due to the fact that the buffers fed into the `put` function need to be crafted to avoid the optimized paths.  
  
We noticed this bug in Boost 1.69, but it's still present in current 1.71 and the [`develop`](https://github.com/boostorg/beast/blob/045dfeb47f6750a940ced9202af0a6331bdcb085) branch.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-18 10:28:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1734#issuecomment-543659675  

This is a case of me being too clever for my own good. The parser should simply require that the input is in a single contiguous buffer. In a future version of Beast I will likely just build the dynamic buffer into the parser itself and keep it flat to hide this implementation detail.

---

## Comment 2

> Username: inetic  
> Created at: 2019-10-18 11:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1734#issuecomment-543699819  

So for completeness, here is a code that reproduces the issue. It's quite ugly because it assumes a particular implementation of `basic_parser::put(ConstBufferSequence const&,...)` and thus I think it's best to just keep it here and not commit to unit tests(?).  
  
```c++  
    void  
    testIssue1734()  
    {  
        // Ensure more than one buffer, this is to avoid an optimized path in  
        // basic_parser::put(ConstBufferSequence const&,...) which avoids  
        // buffer flattening.  
        auto multibufs = [](auto buffers) {  
            std::vector<net::const_buffer> bs;  
            for (auto b : buffers) bs.push_back(b);  
            while (std::distance(bs.begin(), bs.end()) < 2) {  
                bs.push_back({});  
            }  
            return bs;  
        };  
  
        // XXX: This must be the same as basic_parser<...>::max_stack_buffer.  
        // The latter is private however so we can't use it here.  
        std::size_t constexpr max_stack_buffer = 8192;  
  
        // Buffers must be bigger than max_stack_buffer to force flattening  
        // in basic_parser::put(ConstBufferSequence const&,...)  
        std::string first_chunk_data(2*max_stack_buffer + 1, 'x');  
  
        std::string second_chunk_data_part1(max_stack_buffer + 2, 'x');  
        std::string second_chunk_data_part2(max_stack_buffer + 1, 'x');  
  
        multi_buffer b;  
        parser_type<false> p;  
        p.eager(true);  
        error_code ec;  
        std::size_t used;  
  
        ostream(b) <<  
            "HTTP/1.1 200 OK\r\n"  
            "Server: test\r\n"  
            "Transfer-Encoding: chunked\r\n"  
            "\r\n";  
  
        used = p.put(b.data(), ec);  
        b.consume(used);  
  
        BEAST_EXPECT(net::buffer_size(b.data()) == 0);  
        BEAST_EXPECTS(!ec, ec.message());  
        BEAST_EXPECT(!p.is_done());  
        BEAST_EXPECT(p.is_header_done());  
  
        ostream(b) <<  
            std::hex <<  
            first_chunk_data.size() << "\r\n" <<  
            first_chunk_data << "\r\n";  
  
        // First chunk  
        used = p.put(multibufs(b.data()), ec);  
        b.consume(used);  
  
        BEAST_EXPECTS(ec == error::need_more, ec.message());  
        BEAST_EXPECT(!p.is_done());  
  
        ostream(b) <<  
            std::hex <<  
            (second_chunk_data_part1.size() +  
             second_chunk_data_part2.size() ) << "\r\n" <<  
            second_chunk_data_part1;  
  
        // Second chunk, part 1  
        used = p.put(multibufs(b.data()), ec);  
        b.consume(used);  
  
        BEAST_EXPECTS(!ec, ec.message());  
        BEAST_EXPECT(!p.is_done());  
  
        ostream(b) <<  
            second_chunk_data_part2 << "\r\n"  
            << "0\r\n\r\n";  
  
        // Second chunk, part 2  
        used = p.put(multibufs(b.data()), ec);  
        b.consume(used);  
  
        BEAST_EXPECTS(!ec, ec.message()); // <-- Error: bad chunk  
        if(p.need_eof())  
        {  
            p.put_eof(ec);  
            BEAST_EXPECTS(! ec, ec.message());  
        }  
        BEAST_EXPECT(p.is_done());  
    }  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-18 11:59:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1734#issuecomment-543701237  

Just add `friend basic_parser_test;`, this is one of the reasons I use a class for each test suite.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-10-18 12:03:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1734#issuecomment-543702850  

I'll incorporate it and make the adjustment, thanks!
