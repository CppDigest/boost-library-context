# #1199 - Uploading files to beast server [Closed]

> Username: carolinebeltran  
> Created at: 2018-07-19 03:02:08 UTC  
> Updated at: 2018-07-21 15:47:04 UTC  
> Closed at: 2018-07-21 15:47:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1199  

I am currently able to successfully upload a binary file (in MIME format), such as an image to Beast via an html form, e.g.:  
  
```  
<form action="upload" method="post" enctype="multipart/form-data">  
Select image to upload:  
<input type="file" name="fileToUpload" id="fileToUpload">  
<input type="submit" value="Upload Image" name="submit">  
</form>  
```  
  
You can then save the MIME message to a file as follows:  
  
```  
std::ofstream out("output.mime", std::ios::binary);		  
out << req.body();  
```  
  
If you open up the MIME file, you will see a structure similar to:  
  
```  
------WebKitFormBoundaryPSo1mtu2GlpOgY17[CRLF]  
Content-Disposition: form-data; name="fileToUpload"; filename="gordo 2.jpg"[CRLF]  
Content-Type: image/jpeg[CRLF]  
[CRLF]  
THIS IS THE IMAGE BINARY[CRLF]  
------WebKitFormBoundaryPSo1mtu2GlpOgY17[CRLF]  
Content-Disposition: form-data; name="submit"[CRLF]  
[CRLF]  
Upload Image[CRLF]  
------WebKitFormBoundaryPSo1mtu2GlpOgY17--[CRLF]  
  
```  
The embedded binary is an exact binary copy of the originally uploaded image.  
  
Now that we have the image in MIME format, the next step is to extract the binary (and other required metadata pertaining to the file).  This is where my problems begin.

---

## Comment 1

> Username: carolinebeltran  
> Created at: 2018-07-19 03:03:52 UTC  
> Updated at: 2018-07-19 13:56:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1199#issuecomment-406139900  

I realize that I can write some code to parse the MIME myself.  Required fields would be:  
  
Content-Type - allow you to save the file extension such as JPG, PNG, MP4, etc.  
  
Content-Disposition - optional if you need to know the name of the file uploaded by the client.  
  
[CRLF] [CRLF]THE BINARY[CRLF] – The complete binary.  
  
Before going this route, it would be best to find an existing portable MIME library, that is also UTF-8 compatible.

---

## Comment 2

> Username: carolinebeltran  
> Created at: 2018-07-19 03:06:46 UTC  
> Updated at: 2018-07-19 14:06:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1199#issuecomment-406140278  

I looked at some MIME libraries and they are mostly not portable.  Some of them are not moderately sized libraries in my opinion, but rather large frameworks, such as vMime.  I found one called gMime but it appears to be for Linux.  I did find a few smaller and portable libraries, but I don’t feel very comfortable because I found some negative comments regarding problems if the meta data is in UTF-8, e.g. Chinese characters for instance.  
  
Then I found a portable library called Mimetic and built the Windows 'lib' file.  I ran the demos and they work fine.  But there is no demo showing how to extract the binary part of the message (although text based message parts are extracted properly).  
  
Anyway, I’m having difficulty and may be doing something wrong.  Snippet:  
  
```  
// I don't want to mask (or exclude data) for now  
int iMask = mimetic::imNone; //mimetic::imHeader | mimetic::imChildParts | mimetic::imPreamble | mimetic::imEpilogue;  
  
// load message to be parsed by Mimetic  
mimetic::MimeEntity me(req.body().begin(), req.body().end(), iMask);      
```                     
  
I think that my syntax above is correct and can only guess that Mimetic screws up internally above, because I try to print the ‘Content-Type’ and nothing will print:  
  
```  
Header h = me.header();  
ContentType ct = h.contentType();  
cout << ct.type() << "/" << ct.subtype() << endl;  
```  
  
I then save the me.body() to a file and open it up with a hex editor and notice that the binary is NOT the binary received by Beast!  If someone has some working Mimetic code that gets the binary and the necessary meta data, I would really appreciate it!  If I can't get Mimetic working, I will have to parse the MIME myself.  Thank you!
