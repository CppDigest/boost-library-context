# #2426 - Boost post request image data [Closed]

> Username: amarnathreddy0201  
> Created at: 2022-05-16 16:50:29 UTC  
> Updated at: 2022-05-25 08:32:20 UTC  
> Closed at: 2022-05-17 13:36:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2426  

Hi Sir,   
I want to sent image data in boost post request  
  
Data={"photo1", base64_image data}  
  
In a request body  
req.body() =Data  
  
Like this, is it possible?   
  
Thanks,   
ANR S

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-05-16 17:00:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2426#issuecomment-1127913776  

Yes

---

## Comment 2

> Username: amarnathreddy0201  
> Created at: 2022-05-16 17:44:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2426#issuecomment-1127958186  

But when I try this client given True  
Server not getting image

---

## Comment 3

> Username: sehe  
> Created at: 2022-05-16 21:05:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2426#issuecomment-1128134246  

You don't show any working code, so you can't claim useful behaviour for it. If you show your code (preferrably on SO, where I helped you earlier) we can help you spot problems with it, or fill in the missing dots. Right now, your question is too vague to help with and likely not at all related to Beast.

---

## Comment 4

> Username: amarnathreddy0201  
> Created at: 2022-05-17 02:36:20 UTC  
> Updated at: 2022-05-17 05:52:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2426#issuecomment-1128338464  

http::request < http::string_body> req_;  
  
    cv::Mat image = cv::imread("C:/Development/qa-cal/sysqa/gui/download.png");  
    std::vector<unsigned char> buf;  
    cv::imencode(".png", image, buf);  
    auto base64_png = reinterpret_cast<const unsigned char*>(buf.data());  
    std::string image_encode = base64_encode(base64_png, buf.size());  
  
    req_.version(version);  
    req_.method(http::verb::post);  
    req_.target(target);  
    req_.set(http::field::host, host);  
    req_.set(boost::beast::http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req_.set(http::field::content_type, "application/json");  
     
  
    bj::value image_photo = {  
        {"photo1",image_encode}  
    };  
      
  
    req_.body() = bj::serialize(image_photo);//;//bj::serialize(image_photo);  
    req_.prepare_payload();  
  
  
1) on async_write i am include stream(host) and req_ data to on_write .  
  
2) If I send data.  
    bj::value data = {  
        {"photo1",1.3}  
    };  
  
  
   req_.body() = bj::serialize(data);  
   client send data to server , I am getting Success response.  
   Server responding for this request  
  
3) If i try to send image data from client to server, I am get True response.   
Server Response : ImmutableMultiDict([])  
  
4) My hypothesis is am I sending image wrong format.?

---

## Comment 5

> Username: sehe  
> Created at: 2022-05-17 13:29:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2426#issuecomment-1128872022  

What is the question?  
  
You're showing code that looks like it could work. It's a bit inefficient (why read PNG, decoding it, only to encode it again?), and pretty incomplete (where does `base64_encode` come from?  
  
Then  
  
 1. on `async_write` i am include stream(host) and `req_` data to `on_write`   
  
I have no clue what that means. Code talks.  
  
 2.  I am getting Success response. Server responding for this request  
 3. If i try to send image data from client to server, I am get `True` response. `Server Response : ImmutableMultiDict([])`  
  
I have no idea what you're trying to tell me. Great. You get responses. That's probably intended. The server determines what response you should expect.  
  
 4. My hypothesis is am I sending image wrong format.?  
  
I have no idea what gives you the hypothesis. You send data. You get responses. We can verify neither.  
  
I'm closing this issue as this is not the place for general programming questions, or debugging your client code. If you post your question on StackOverflow I'll be happy to show it in case it helps (including the GIL usage that you posted another question about on SO). .

---

## Comment 6

> Username: amarnathreddy0201  
> Created at: 2022-05-25 08:32:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2426#issuecomment-1136953731  

The above procedure is correct and in server side we want to do base64decode and cv::imdecode in cpp
