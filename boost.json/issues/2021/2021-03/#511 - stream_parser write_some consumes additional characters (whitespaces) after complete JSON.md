# #511 - stream_parser write_some consumes additional characters (whitespaces) after complete JSON [Closed]

> Username: mateuszuram  
> Created at: 2021-03-01 11:54:04 UTC  
> Updated at: 2021-03-04 05:28:55 UTC  
> Closed at: 2021-03-04 05:28:55 UTC  
> Url: https://github.com/boostorg/json/issues/511  

Hi,  
  
First of all thanks for sharing this library it's really cool.  
I found a small problem that I guess depending on a context might be a bug or a feature :).  
  
### Version of Boost  
1.75.0  
  
### Steps necessary to reproduce the problem  
In below program '\t' will be consumed by <write_some>  
{  
    boost::json::stream_parser p;  
    std::string_view str = "{\"key\": \"val\"}\t";  
    auto w = p.write_some(str);  
    fmt::print("Write: {} chars\n", w);  
}  
  
### All relevant compiler information  
Any  
  
This is a problem in case if communication protocol require those bytes to be present.  
For example we my have: <JSON header><optional binary data>, in this case depending what kind of binary data we are sending some characters from the beginning might get consumed.  
  
Documentation doesn't clearly states that those characters will get consumed:  
"The write_some function is an alternative which allows the parse to finish early, without consuming all the characters in the buffer. This allows parsing of a buffer containing multiple individual JSONs or **containing different protocol data:** "  
  
I guess this can be figured out base on the example but it will be nice if it was clearly said.  
n = p.write_some( ",3,4] null" );               // parse the remainder of the JSON  
assert( n == 6 );                               // only some characters consumed

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-03-01 13:04:59 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-787933837  

According to JSON specification (https://tools.ietf.org/html/rfc7159#section-2)  
```  
JSON-text = ws value ws  
```  
which means, all whitespace after the top-level value is part of the document. If the JSON document in a stream is followed by other data, it has to be separated by something other than whitespace.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-03-01 14:42:02 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-788000263  

Working as designed. Trailing whitespace is part of a JSON.

---

## Comment 3

> Username: mateuszuram  
> Created at: 2021-03-01 15:10:29 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-788022414  

Yes I agree but leading white-spaces are also part of JSON.  
So if we have stream like this: '{"a": "b"}  {"c":"d"}'  
We can divided on 2 JSON like this: '{"a": "b"}  ' and '{"c":"d"}', or like this: '{"a": "b"}' and '  {"c":"d"}'  
I guess it all goes down to specification if "write_some" should be greedy and consume as much characters from a stream as possible for a valid JSON or it should be non-greedy and consume as little as possible.  
I think in case of stream_parser it should be non-greedy or maybe we can have additional function that will be non greedy?

---

## Comment 4

> Username: grisumbras  
> Created at: 2021-03-01 15:13:59 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-788024991  

`write_some` consumes the whole document, including whitespace. In your example, the whitespace between 2 JSON objects belongs to the first document, _according to JSON spec_.

---

## Comment 5

> Username: mateuszuram  
> Created at: 2021-03-02 18:57:44 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-789136348  

Ok but in that case shouldn't `done ` function return true only if it parse valid JSON and it finds first non white-space character in the stream or steam will finish? Like in below example:  
```  
    std::string_view stream = R"(  {"key1": "val1"}  )";  
    std::string_view stream_continue = R"(  {"key2": "val2"}  )";  
    boost::json::stream_parser jp;  
    jp.write_some(stream);  
    if (jp.done()) {  // This should be false since we don't know yet if there are more white-spaces in the stream or not  
      fmt::print("First parse: {}\n", boost::json::serialize(jp.release()));  
      jp.reset();  
    }  
    auto num_parsed = jp.write_some(stream_continue );  // This should consume 2 leading white-spaces and exit (now we reached complete JSON)  
    if (jp.done()) {  // This should be true now  
      fmt::print("Second parse: {}\n", boost::json::serialize(jp.release()));  
      jp.reset();  
    }  
    auto stream_continue2 = stream_continue .substr(num_parsed);  
    jp.write_some(stream_continue2 );  // This should parse second JSON  
    jp.finish();  
    if (jp.done())  // This should be true since the stream finished  
      fmt::print("Third parse: {}\n", boost::json::serialize(jp.release()));  
```  
  
Currently the fact if trailing white-spaces will belong to the first document or second depends on how we are going to cut the stream, if we for example cut it like this:  
```  
    std::string_view stream = R"(  {"key1": "val1"})";  
    std::string_view steram_continue = R"(      {"key2": "val2"}  )";  
```  
All white-spaces will belong to second document.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-03-02 19:05:36 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-789141590  

> auto num_parsed = jp.write_some(stream_continue );  // This should consume 2 leading white-spaces and exit (now we reached complete JSON)  
  
The statement "now we reached complete JSON" is incorrect. When using `stream_parser`, you have to call `finish` in this case to indicate that there is no more data. This is described in the documentation for `write_some`:  
  
```  
        To indicate there are no more character buffers,  
        such as when @ref done returns `false` after  
        writing, call @ref finish.  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-03-04 05:28:55 UTC  
> Url: https://github.com/boostorg/json/issues/511#issuecomment-790303770  

I believe this is resolved. If you think there is still a problem please feel free to open a new issue, thanks!
