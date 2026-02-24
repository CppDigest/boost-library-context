# #661 - stream_parser example with fstream? [Open]

> Username: sinall  
> Created at: 2021-12-21 04:15:55 UTC  
> Updated at: 2023-06-29 08:25:32 UTC  
> Url: https://github.com/boostorg/json/issues/661  

Since `file.hpp` is not part of this library, it's not convenient to be used.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-12-21 06:59:52 UTC  
> Url: https://github.com/boostorg/json/issues/661#issuecomment-998522678  

I'm not sure if I understand your issue correctly. Are you asking for an example of using the library for parsing from `std::fstream`?

---

## Comment 2

> Username: oxygene  
> Created at: 2023-06-29 08:10:27 UTC  
> Url: https://github.com/boostorg/json/issues/661#issuecomment-1612602385  

I'm not the original poster, but I assume I have the same "problem". It's very convenient to parse a whole std::string. And if I want to parse the contents of an std::ifstream, the easiest approach would be to slurp in the contents of the whole file into a std::string `std::string{istreambuf_iterator{file}, {}}` and use `boost::json::parse` again.  
  
However, if I don't want to first read the contents of the whole file into memory, I need a streaming parser. This is where the stream_parser example comes into play. I assume the OP wanted to apply this example to a standard `std::ifstream` because this is a very common use-case.  
  
I agree that having an example just based on `std::ifstream` would be better because people usually don't invent their own file io classes. Additionally, having convenience functions for streaming to and from `std::ostream` and `std::istream` would be great and would take the burden from the users to write the boilerplate "pumping" code to/from the streams.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-06-29 08:25:31 UTC  
> Url: https://github.com/boostorg/json/issues/661#issuecomment-1612621225  

Since version 1.81.0, the simplest (and, for most cases, the best) way to parse an `std::istream` is `operator>>`.  
  
E.g.  
```  
std::ifstream is(filename);  
boost::json::value jv;  
is >> jv;  
```  
  
`operator<<` for value also exists.
