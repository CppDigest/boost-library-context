# #619 - No simple way to parse from std::istream [Closed]

> Username: grisumbras  
> Created at: 2021-09-30 16:41:31 UTC  
> Updated at: 2022-11-05 14:08:18 UTC  
> Closed at: 2022-11-05 14:08:18 UTC  
> Url: https://github.com/boostorg/json/issues/619  

There should be a quick and convenient way to parse JSON from `std::istream`. E.g.   
```c++  
std::ifstream f("path/to/file");  
auto jv = json::parse(f);  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-02 16:47:16 UTC  
> Url: https://github.com/boostorg/json/issues/619#issuecomment-932782276  

> there should be parse_istream and there should even be parse_file that takes the file name directly  
  
Well, `parse_istream` can just be a `parse` overload.

---

## Comment 2

> Username: prince-chrismc  
> Created at: 2021-10-09 03:29:03 UTC  
> Url: https://github.com/boostorg/json/issues/619#issuecomment-939216210  

We have [`operator<<`](https://www.boost.org/doc/libs/1_77_0/libs/json/doc/html/json/ref/boost__json__operator_lt__lt_.html) for serializing but no `operator>>` for parsing  
  
It's be great to have  
  
```cpp  
std::istringstream iss{"[1,2,3,4,5]"};  
value jv;  
iss >> jv;  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-09 03:31:40 UTC  
> Url: https://github.com/boostorg/json/issues/619#issuecomment-939216523  

@prince-chrismc I agree! Would you like to try your hand at it? Shouldn't be too hard...

---

## Comment 4

> Username: grisumbras  
> Created at: 2021-10-10 09:50:52 UTC  
> Url: https://github.com/boostorg/json/issues/619#issuecomment-939443718  

I have a blueprint for the fix. I originally wanted to make the streambuffer to put all input into parser as soon as possible which would work OK with something like `istream >> &json_buffer`. But then I realized that  
```c++  
std::copy(  
  std::istreambuf_iterator<char>(istream),  
  std::istreambuf_iterator<char>(),  
  std::ostreambuf_iterparsing(&json_buffer))  
```  
will push input into parser one byte a time, which is very ineffective.  
So, my approach has to be changed to have a  proper buffer in the streambuf.
