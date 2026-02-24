# #1635 - Client (javascript) unable to receive image or binary from server using boost beast and opencv [Closed]

> Username: ericjansen  
> Created at: 2019-06-11 07:27:39 UTC  
> Updated at: 2019-07-18 09:01:07 UTC  
> Closed at: 2019-07-18 09:01:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1635  

Hi @vinniefalco   
  
I am creating a full-duplex between html client javascript websocket - C++ boost::beast websocket + OpenCV.   
Basically the logic is to upload image from client-side and being processed on server-side, and server replies back to client-side to display the processed image.  
I see it is generating image file on client-side but it's a broken image. I also added `binary(true)`.  
I modified your chat client source code therefore it can send the image to server, as shown below:  
```  
var canvas = document.createElement("canvas");  
var ctx = canvas.getContext("2d");  
var img = document.getElementById("image");  
  
var ws = null;  
connect.onclick = function() {  
   ws = new WebSocket(uri.value);  
   ws.onopen = function(ev) {  
     messages.innerText += "[connection opened]\n";  
   };  
   ws.onclose = function(ev) {  
     messages.innerText += "[connection closed]\n";  
   };  
   ws.onmessage = function(ev) {  
     messages.innerText += ev.data + "\n";  
   };  
   ws.onerror = function(ev) {  
     messages.innerText += "[error]\n";  
     console.log(ev);  
   };  
 };  
disconnect.onclick = function() {  
   ws.close();  
};  
send.onclick = function() {  
   var canvas1 = ctx.getImageData(0,0,img.width,img.height);  
   var binary = new Uint8Array(img.data.length);  
   for (var i=0; i<img.data.length; ++i) {  
     binary[i] = img.data[i];  
   }  
  
   ws.send(binary.buffer);  
}  
```  
The server side source code I tried and modified your sample source code "Web server Asynchronous" - on_write function:  
```  
void on_write(boost::system::error_code ec,std::size_t bytes_transferred) {  
   boost::ignore_unused(bytes_transferred);  
   if(ec)  
     return fail(ec, "write");  
   buffer_.consume(buffer_.size());  
   std::cout << "on write before do read\n";  
   ws_.async_read(buffer_,  
               boost::asio::bind_executor(strand_,std::bind(&session::on_read, shared_from_this(),   
                   std::placeholders::_1,std::placeholders::_2)));  
   uint8_t *buf = new uint8_t [bytes_transferred];  
  
boost::asio::buffer_copy(boost::asio::buffer(buf,bytes_transferred),buffer_.data(),bytes_transferred);  
        ::cv::Mat mImg(400,320,CV_8UC3,buf);  
        ::cv::imwrite("file.jpg",mImg);  
      delete [] buf;  
}  
```  
Appreciate your assistance. Thanks

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-07-11 08:32:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1635#issuecomment-510387721  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-07-18 09:01:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1635#issuecomment-512730001  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
