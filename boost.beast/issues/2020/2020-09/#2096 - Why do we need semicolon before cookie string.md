# #2096 - Why do we need semicolon before cookie string [Closed]

> Username: yuntonyx  
> Created at: 2020-09-19 04:28:17 UTC  
> Updated at: 2021-03-16 12:13:59 UTC  
> Closed at: 2020-12-25 12:13:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2096  

As I was trying to parse cookies in beast. I used the reference here: https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__http__param_list.html  
```c++  
for(auto const& param : boost::beast::http::param_list{";level=9;no_context_takeover;bits=15"})  
{  
        std::cout << ";" << param.first;  
        if(! param.second.empty())  
            std::cout << "=" << param.second;  
        std::cout << "\n";  
}  
```  
It seems that param_list assumes that each cookie value should start with a semicolon. However, this is not the case when browsers send the actual cookies. Therefore the following code produces no output (taken from https://github.com/boostorg/beast/issues/1425#issuecomment-459985055) :  
```c++  
for (auto param : boost::beast::http::param_list(m_req[boost::beast::http::field::cookie])) {  
     std::cout << "Cookie '" << param.first << "' has value '" << param.second << "'\n";  
}  
```  
  
but the following code prints out the cookies correctly:  
```c++  
for (auto param : boost::beast::http::param_list(";" + m_req[boost::beast::http::field::cookie].to_string())) {  
     std::cout << "Cookie '" << param.first << "' has value '" << param.second << "'\n";  
}  
```  
  
I am wondering why this is the intended behavior?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-19 06:03:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-695170508  

Beast is a low level library and makes no attempt to parse the cookie headers. param_list is not intended for this.

---

## Comment 2

> Username: AeroStun  
> Created at: 2020-10-06 03:10:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-704001765  

Would Beast welcome cookie parsing primitives? I have a design and implementation from a couple months ago lying around; still completely missing serializing tho

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-727717653  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-12-25 12:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-751240979  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 5

> Username: Tectu  
> Created at: 2021-03-16 10:14:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-800130442  

> Would Beast welcome cookie parsing primitives? I have a design and implementation from a couple months ago lying around; still completely missing serializing tho  
  
Any chance that you can make it available through posting it as a gist, blog post, repository or something? Would be really interested to give it a look to see how much work it would save me :)

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-03-16 10:18:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-800133209  

I think quite a few of our users would welcome this, as to whether it's something that beast should be proividing is probably a question for @vinniefalco

---

## Comment 7

> Username: AeroStun  
> Created at: 2021-03-16 11:55:23 UTC  
> Updated at: 2021-03-16 12:13:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2096#issuecomment-800194993  

I have [this](https://github.com/AeroStun/beast/commits/cookies) lying around my account; spent my whole last August on it; IIRC it works.  
  
You are probably interested in `list_all_cookies` from my second commit
