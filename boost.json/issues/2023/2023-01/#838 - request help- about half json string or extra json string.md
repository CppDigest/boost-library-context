# #838 - request help: about half json string or extra json string [Closed]

> Username: zhixiongdu027  
> Created at: 2023-01-13 10:53:38 UTC  
> Updated at: 2023-01-16 02:34:29 UTC  
> Closed at: 2023-01-16 02:34:29 UTC  
> Url: https://github.com/boostorg/json/issues/838  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
1.75  
You can find the version number in the file `<boost/version.hpp>`  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!  
  
  
### Request Help  
  
Hello:  
  
When dealing with network data, the following situations are often encountered  
  
1: The json string has only read half of the content, and the remaining content needs to be read from the socket again.  
2: Two or more json strings are combined and returned  
  
Can boost json handle this situation?  
  
TKS

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-01-13 11:12:23 UTC  
> Url: https://github.com/boostorg/json/issues/838#issuecomment-1381697094  

If I understand you correctly, then  
1. Yes, see https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/input_output.html#json.input_output.parsing.streaming_parser  
2. Also yes, see https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/ref/boost__json__stream_parser/write_some/overload1.html

---

## Comment 2

> Username: zhixiongdu027  
> Created at: 2023-01-13 11:32:48 UTC  
> Url: https://github.com/boostorg/json/issues/838#issuecomment-1381726418  

Thanks for the answer, I have checked the link you gave. But I don't think it's what I want  
  
The question is this:  
  
If the response data from the server is  
  
 {"k1":"v1"}\r\\n{"k2":"v2"}\r\\n{"k3":"v3"}\r\\n  
  
Obviously, we can parse out the three json:value  
  
You should know that the length of the data that can be read from the server at one time is unknown,  
  
  
  
But I did not find a way to support this kind of data analysis in the way of using stream_parser

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-01-13 11:45:18 UTC  
> Url: https://github.com/boostorg/json/issues/838#issuecomment-1381744240  

This is exactly what https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/ref/boost__json__stream_parser/write_some/overload1.html is designed to handle.  
It should look a bit like this  
```cpp  
stream_parser p;  
std::size_t n;  
error_code ec;  
while( buffer_size > 0 )  
{  
    n = p.write_some( buffer, buffer_size, ec );  
    if( ec ) { /* handle error */ }  
      
    buffer += n;  
    buffer_size -= n;  
      
    if( p.done() ) // full JSON document was parsed  
    {  
        value jv = p.release();  
        // use value somehow  
        parser.reset();  
        continue; // this will restart loop and potentially start parsing the next value  
    }  
}  
```

---

## Comment 4

> Username: zhixiongdu027  
> Created at: 2023-01-13 11:48:53 UTC  
> Updated at: 2023-01-16 01:18:09 UTC  
> Url: https://github.com/boostorg/json/issues/838#issuecomment-1381747289  

It works for me .many tks   ~_~
