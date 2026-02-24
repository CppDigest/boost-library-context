# #1624 - how can i reference the header parameter in reader's constructor of body while its finish being invoked [Closed]

> Username: yiivon  
> Created at: 2019-05-28 19:55:11 UTC  
> Updated at: 2019-07-04 22:35:00 UTC  
> Closed at: 2019-07-04 22:35:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1624  

I really want to how can i reference the parameter **h**(_header<isRequest, Fields>_) in reader's constructor of body while its finish being invoked:  
`  
class reader  
  | {  
  | value_type& body_;  
  |    
  | public:  
  | template<bool isRequest, class Fields>  
  | explicit  
  | reader(header<isRequest, Fields>& /**  h **/, value_type& b)  
  | : body_(b)  
  | {  
  | }  
  
`  
  
Thanks so much!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-05-28 19:56:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1624#issuecomment-496665017  

Well first you need to give it a name. `h` seems prudent, perhaps remove those comments. Then access it the same way you would access any other variable.

---

## Comment 2

> Username: yiivon  
> Created at: 2019-05-28 20:11:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1624#issuecomment-496670827  

> Well first you need to give it a name. `h` seems prudent, perhaps remove those comments. Then access it the same way you would access any other variable.  
  
So surprised and touched my header so much! Thank you a lot! but i need to visit that variant **h** later in reader's member finish, so maybe i need to hold the reference of **h** inner class reader, but don't know the finall type of header<isRequest, Fields> , and the reader of body is checked in the parser with `static_assert(is_body_reader<Body>::value, "BodyReader type requirements not met");`, i don't know how to hold **h** in class reader so that it can be used later?  
  
Thanks again, and maybe this question take your times, then so sorry about that and just ignoring, i am thankful so much still!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-05-28 21:03:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1624#issuecomment-496689144  

>  i need to visit that variant h later in reader's member finish, so maybe i need to hold the reference of h inner class reader, but don't know the finall type of header<isRequest, Fields>  
  
Yeah, this is a problem / limitation with the current API.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-06-27 21:39:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1624#issuecomment-506521757  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-07-04 22:34:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1624#issuecomment-508585900  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
