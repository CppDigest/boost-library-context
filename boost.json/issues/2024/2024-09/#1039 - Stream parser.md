# #1039 - Stream parser? [Closed]

> Username: sabudilovskiy  
> Created at: 2024-09-11 09:26:52 UTC  
> Updated at: 2024-10-09 13:12:37 UTC  
> Closed at: 2024-10-09 13:12:36 UTC  
> Url: https://github.com/boostorg/json/issues/1039  

Are there any plans to add parse_one_token methods to the parser? This would greatly simplify the writing of good domeless parsers, since processing would become much easier. The current api not only does not allow this to be done legally, but also illegally - it is simply impossible, since the parser behaves very strangely to receive false from the handler to process the end of the array/object.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-09-11 10:23:48 UTC  
> Url: https://github.com/boostorg/json/issues/1039#issuecomment-2343250786  

We have a [`stream_parser`](https://www.boost.org/doc/libs/1_86_0/libs/json/doc/html/json/ref/boost__json__stream_parser.html) class, but it's not what you are talking about. It parses a JSON document until it runs out of input, then suspends.  
  
We also have an API to handle JSON parsing events. The keystone of it is the class template [`basic_parser`](https://www.boost.org/doc/libs/1_86_0/libs/json/doc/html/json/ref/boost__json__basic_parser.html). You can read an explanation of its usage in [this section](https://www.boost.org/doc/libs/1_86_0/libs/json/doc/html/json/input_output.html#json.input_output.parsing.custom_parsers).  
  
We also support parsing without constructing DOM containers. You can read about it [here](https://www.boost.org/doc/libs/1_86_0/libs/json/doc/html/json/conversion/direct_parsing.html).  
  
So, what specifically are you requesting? The ability to suspend parsing on DOM events? If so, what are you trying to achieve?

---

## Comment 2

> Username: sabudilovskiy  
> Created at: 2024-09-11 11:36:04 UTC  
> Updated at: 2024-09-11 11:41:15 UTC  
> Url: https://github.com/boostorg/json/issues/1039#issuecomment-2343418977  

I have seen this api, I am aware that it exists. But if you use it to parse complex types (with nesting and so on), then in this handler you have to assemble a stack of parsers in one way or another. It even seems boost::parse_into works the same way, I've seen that parsers in types keep pointers on parents (and on members?). I'm not sure. I have an idea to do this explicitly if the parser will be able to receive exactly 1 token. Then, for example, when parsing the aggregate, I can go into recursion, and the parse stack will assemble on its own.  
  
>The ability to suspend parsing on DOM events? If so, what are you trying to achieve?  
  
Yep, i want read_some_event in parser. The usage should have been something like this (in fact, a wrapper over basic_parser is already used here, it would be enough for me to be able to read exactly 1 token, I will do the rest myself).  
  
```  
void parse(stream_parser& parser) {  
    using wait = wait_handler::wait_e;  
    wait_handler& handler = parser.p.handler();  
  
    parser.consume(wait::object_begin);  
    
    while (true){  
      parser.consume(wait::key | wait::object_end);  
      if (handler.got == wait::object_end){  
        break;  
      }  
      cur_ = count_index(handler.key_m);  
  
      if (cur_ == empty){  
          BOOST_JSON_FROM_ERROR;          
      }  
      else if (cur_ == unknown){  
        ingore_parser{}.parse(parser);   
      }  
      else {  
        if (parsed_[cur_]){  
            BOOST_JSON_FROM_ERROR;  
        }  
        visit_index<N - 1>([&]<std::size_t I>(){  
          using Field = boost::pfr::tuple_element_t<I, T>;  
          auto& field = boost::pfr::get<I>(t_);  
          boost_domless_parser<Field>{field}.parse(parser);  
        }, cur_);  
          parsed_[cur_] = true;  
          cur_ = empty;  
        }        
    }  
    if (!all_parsed()){  
      BOOST_JSON_FROM_ERROR;  
    }  
  }  
  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-09-11 16:32:00 UTC  
> Url: https://github.com/boostorg/json/issues/1039#issuecomment-2344133705  

That is a useful feature but it is something completely separate from what Boost.JSON offers. It could be a different set of algorithms.

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-09-11 16:53:22 UTC  
> Url: https://github.com/boostorg/json/issues/1039#issuecomment-2344181811  

So, you want parsing directly into a user defined type, but parse_into doesn't work for you. Can you elaborate why?

---

## Comment 5

> Username: sabudilovskiy  
> Created at: 2024-09-12 10:46:07 UTC  
> Url: https://github.com/boostorg/json/issues/1039#issuecomment-2345941933  

> So, you want parsing directly into a user defined type, but parse_into doesn't work for you. Can you elaborate why?  
  
I didn't say that parse_into didn't work -I just wasn't going to use it. I haven't measured it yet, but maybe parse_into will be good enough (at the moment I don't have an implementation for it). I would just like to have an additional api with parsing of one token, the code that I saw in the parser seems to change the state very often at once, which is why actually there may have to change quite a lot for this. Attempts to illegally achieve the desired result failed, so I decided to write here. It seems to me that the approach with parsing exactly one token is much more convenient for custom domeless parsers than a global handler (because, as I said, you can build recursion for nested types without an explicit stack, only on the call stack).

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-10-09 13:12:36 UTC  
> Url: https://github.com/boostorg/json/issues/1039#issuecomment-2402314543  

As an experiment I changed the parser to allow resuming after the handler returns `false`. The change is in #1045. As you can see on that page it negatively affects performance across the board. And I don't think the result is very convenient (https://github.com/boostorg/json/pull/1045/files#diff-900c7cb3fad3cc2e8fc455f79befd13a2d53517d4e9dac9c7076bc96cbacb428R1968-R1998).  
  
I'm inclined to say that our parser is simply not designed for this approach, so I'm closing this issue as not planned.
