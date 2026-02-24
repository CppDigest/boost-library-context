# #664 - How to decompress gzip encoded response properly? [Closed]

> Username: WeissGotsman  
> Created at: 2017-07-17 15:05:25 UTC  
> Updated at: 2017-07-18 14:21:43 UTC  
> Closed at: 2017-07-17 17:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/664  

for beast version 76,   
  
the following code is modified from the http client example, with gzip enabled. but I failed to decompress it either with the boost iostream interface or zlib interface, I also tried to save the response body string into a .gz file and try to decompress it with 7-zip, but get a CRC error.  
  
https://pastebin.com/DiUME7Z1  
  
My question is: is the response body in standard gzip format, when Content-Encoding: gzip, how to decompress it properly? Most http request library does not require manual decompression.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-17 15:34:32 UTC  
> Updated at: 2017-07-17 15:34:41 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315790906  

>Most http request library does not require manual decompression.  
  
Beast only removes the chunk-encoding. You are responsible for everything else, including gzip. Beast is not a "http request library" its a low-level protocol layering on top of HTTP. I don't know the details of gzip encoding so I'm afraid I won't be of much help. However, if you get it working you should consider publishing it as part of a utilities library that works with Beast.

---

## Comment 2

> Username: WeissGotsman  
> Created at: 2017-07-17 16:01:09 UTC  
> Updated at: 2017-07-17 16:01:39 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315798269  

I tried to compress the std::string of the uncompressed http response body (from www.example.com) with the boost iostream interface using gzip, the first 10 bytes differ from the gzipped http response body

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-17 16:04:27 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315799204  

Perhaps this is related to the "gzip header?" Does HTTP use the header when using the gzip transfer-coding? These links might help:  
https://stackoverflow.com/questions/5280633/gzip-compression-of-chunked-encoding-response  
https://stackoverflow.com/questions/1838699/how-can-i-decompress-a-gzip-stream-with-zlib  
If you use the straight zlib API, you can add 32 to windowBits to auto-detect the header, as mentioned in the link immediately above.

---

## Comment 4

> Username: WeissGotsman  
> Created at: 2017-07-17 16:20:19 UTC  
> Updated at: 2017-07-17 16:21:24 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315803637  

It looks like the www.example.com server only use "Content-Encoding: gzip".   
There is no "Transfer-Encoding: gzip" found in the response

---

## Comment 5

> Username: WeissGotsman  
> Created at: 2017-07-17 16:34:53 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315807698  

the answer in your second link is correct, change line 42 of my code from  
`if (inflateInit(&zs) != Z_OK)`  
to  
`if (inflateInit2(&zs, MAX_WBITS + 16) != Z_OK)`  
works

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-17 16:58:21 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315814568  

Aha!!! Nice!!

---

## Comment 7

> Username: WeissGotsman  
> Created at: 2017-07-17 17:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315818522  

a production ready solution:  
  
https://github.com/curl/curl/blob/master/lib/content_encoding.c  
https://github.com/curl/curl/blob/master/lib/content_encoding.h

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-17 18:20:33 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-315838410  

I don't see how that integrates with Beast?

---

## Comment 9

> Username: WeissGotsman  
> Created at: 2017-07-18 14:21:42 UTC  
> Url: https://github.com/boostorg/beast/issues/664#issuecomment-316079451  

CURL's code serves as a reference on how to setup the zlib correctly, even through boost::iostreams  
  
	//decompress with boost's interface  
	boost::iostreams::array_source src{ res.body.data(), res.body.size() };  
	boost::iostreams::filtering_istream is;  
  
	//boost::iostreams::zlib_params zparams{};  
	if (res["Content-Encoding"] == "deflate")  
	{  
		std::cout << "decompressing " << res["Content-Encoding"] << std::endl;  
		std::cout << "-------------------------------------------------" << '\n';  
		is.push(boost::iostreams::zlib_decompressor{ -MAX_WBITS });			// deflate  
	}  
	else if (res["Content-Encoding"] == "gzip")  
	{  
		std::cout << "decompressing " << res["Content-Encoding"] << std::endl;  
		std::cout << "-------------------------------------------------" << '\n';  
		is.push(boost::iostreams::gzip_decompressor{});						// gzip  
	}  
	else if (res["Content-Encoding"] == "")  
	{  
		std::cout << "uncompressed " << res["Content-Encoding"] << std::endl;  
		std::cout << "-------------------------------------------------" << '\n';  
	}  
  
	is.push(src);		  
	boost::iostreams::copy(is, std::cout);
