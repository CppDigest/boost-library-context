# #2669 - who to get the Data from DynamicBuffer [Closed]

> Username: FriNagy  
> Created at: 2023-04-17 19:30:43 UTC  
> Updated at: 2023-05-15 07:42:29 UTC  
> Closed at: 2023-05-15 07:42:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2669  

Hello,   
first I can compile and run this project and works all as expected, fast and robust, congratulations.  
I use VS2022, with the debugger I can run and inspect step by step.  But unfortunately no chance to understand what is going on.   
(I write my tools in C and have little experience with C++).  
     
I just want to get the data from the read buffer into a char[1024] variable,   
  
in read.hpp   
  
```  
public:  
    read_some_op(  
        AsyncReadStream& s,  
        DynamicBuffer& b,  
        basic_parser<isRequest>& p)  
        : s_(s)  
        , b_(b)  
        , p_(p)  
  
  ....      
  
     b_.commit(bytes_transferred);  
  
//    the data is there, now I try to copy this to my  Variable  
//    but NOTHING WORKS  
   
       char mycstri[1024];  
  
       memcpy( mycstri, b_.begin_ , bytes_transferred);  
       memcpy( mycstri, b_.data() , bytes_transferred);  
       memcpy( mycstri, reinterpret_cast<const char *>(b_.data() ), bytes_transferred);  
  
//       beast::make_printable(b_.data())  
  
  
```  
after 4 hours of searching and trying I have no chance to access it somehow, nothing works  
  
can someone help, please   
TIA

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-04-17 19:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2669#issuecomment-1511983222  

I remember when I first tried Boost.Asio, like idk.... I think it was actually 19 years ago, and I had this exact problem. I ended up avoiding it for years because of it. Can you show your code? Generally speaking if you want to copy out the data you might write:  
```  
char buf[ 256 ];  
DynamicBuffer db;  
...  
std::size_t amount_copied = boost::asio::buffer_copy(  
    boost::asio::buffer( buf ), db.data() );  
```  
  
But it would be easier for you to just use the `beast::flat_buffer` class which guarantees that it returns its results in a single memory region:  
  
```  
boost::beast::flat_buffer fb;  
...  
boost::beast::flat_buffer::const_buffers_type cb = fb.data();  
  
std::string_view s( reinterpret_cast<char const*>( cb.data() ), cb.size() );  
```  
  
Now you have a string view, which is better than making an unnecessary copy of the data.  
  
Hope this helps!

---

## Comment 2

> Username: FriNagy  
> Created at: 2023-04-17 20:46:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2669#issuecomment-1512063386  

Hello,   
  
Excellent, just as I wanted, thank you so much.  
  
I will also give a try with the string view  
 but at 66 years, the chances of me getting further are not so good :(

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-04-17 23:44:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2669#issuecomment-1512242051  

You can replace `std::string_view` with `std::string` in the code I posted, and it will work the same way.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-05-15 01:45:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2669#issuecomment-1547079087  

@FriNagy is this issue resolved?

---

## Comment 5

> Username: FriNagy  
> Created at: 2023-05-15 07:42:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2669#issuecomment-1547345890  

yes, is solved  
  
I get so the request filename in the read.hpp  
  
```  
if (bytes_transferred) {  
  
    boost::beast::flat_buffer::const_buffers_type cb = b_.data();  
    std::string_view s(reinterpret_cast<char const*>(cb.data()), cb.size());  
  
    std::size_t httpfound = s.find(" HTTP");  // search in header  
  
    if (httpfound != std::string::npos) {  
       if (httpfound < 100) {  
            char  dest[100];  
            std::copy(s.begin(), s.begin() + ++httpfound, dest);  
            dest[httpfound] = '\0';  
  
```        
  
Thank you!
