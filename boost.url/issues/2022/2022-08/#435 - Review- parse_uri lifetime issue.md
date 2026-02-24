# #435 - Review: parse_uri lifetime issue [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 01:07:47 UTC  
> Updated at: 2022-09-13 17:31:42 UTC  
> Closed at: 2022-09-11 23:52:06 UTC  
> Url: https://github.com/boostorg/url/issues/435  

>   auto r = parse_uri( s );  
>   
> There's no indication here that r is a non-owning view and that  
> the caller is responsible for keeping s alive for the lifetime of r.  
  
> A safer API design would, at a minimum, give the parsing function  
> a name with "view" in it. The default, shorter-named function  
> should return a value type.  
  
Some solutions would be:  
  
```cpp  
auto r = parse_uri_view( s );  
```  
  
The biggest problem is deciding what   
  
```cpp  
auto r = parse_uri( s );  
```  
  
should return. The non-ambiguous solution would be  
  
```cpp  
url u;  
auto r = parse_uri( s, u );  
```  
  
or a static member function forcing the user to type the type:  
  
```cpp  
auto r = url::parse_uri( s );  
```  
  
The solution `std::ranges` uses is the `views` namespace, which would be:  
  
```cpp  
auto r = urls::views::parse_uri( s );  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-22 01:11:26 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1221674057  

To me the problem is `auto`. In other words, the issue described here is not specific to URLs; it is general for any user that uses `auto` to capture the result of a function that returns a reference-like type over its arguments.  
  
Instead of burdening every function that has ever been written, has been written, and will ever be written with the responsibility of communicating its return type twice - once in the actual return type and again in the function name, it seems to me that a more logical solution is that the author of the code chooses whether to opt-in to brevity (using `auto`) or opt-in to clarity (naming the return type).

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-22 04:54:41 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1221821911  

I just noticed about another problem with `parse_uri_view(...)` or whatever. It makes it less convenient and doesn't solve much because it just pushes the problem deeper when we access the URL components.  
  
To completely achieve the requirement in the review of `auto x = ...` not tricking "beginners" we would need some kind of:  
  
```cpp  
url u = parse_uri(...);  
std::string h = u.host();  
```  
  
so the user can  
  
```cpp  
url u = parse_uri(...);  
auto h = u.host();  
```  
  
to keep "beginners" from the lifetime issues when using `auto`, which was the original requirement.   
  
Then we would still need the views for "non-beginners", and this would become lots of variants:  
  
```cpp  
std::string h1 = u.host();  
std::string h2 = u.encoded_host();  
pct_encoded_string h3 = u.host_view();  
string_view h4 = u.encoded_host_view();  
```  
  
I foresee these extra functions annoying most of the "non-beginners".

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-22 05:52:17 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1221869393  

More comments  
  
> Overall, I quite like the design.  Lazy, lazy, lazy.  The fact that  
only views into the underlying sequence are returned from the parse is  
great, and the fact that there are both owning (url::url and  
fixed-capcity owning url::static_url) and view versions of the URL  
representations is exactly what I would need as a user.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-22 15:59:34 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222559896  

More comments:  
  
> Overall, I found the API fairly easy to use. I understand the concern about  
> parsing into a view type, but I consider it a lesser evil than allocating on  
> every parse or creating two kinds of functions, one that allocates and one that  
> doesn't.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-08-22 16:11:36 UTC  
> Updated at: 2022-08-22 16:22:02 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222577255  

More comments:  
  
> I feel that maybe what I'm saying is not getting through:  
>   
> *I am not insisting that you remove these dangerous functions. You  
> are welcome to keep them for those users who feel that they need the  
> benefits that they offer. I would just like you to *also* offer  
> safe-to-use functions, and to give the safe functions more concise  
> names than the dangerous functions (and mention them first in the  
> docs).*  
  
> > Yes well, we considered that design and concluded that it wasn't  
> > practical. The library has multiple value types for URLs, which one  
> > should it return?  
  
> One of the safe ones. I'd suggest one that uses a std::string to store  
> the URL.  
  
> No, I'm NOT SAYING THAT IT SHOULD NOT LET THE USER DO THAT, I'm saying  
> that a user who wants to do that should have to do a bit more typing to  
> make it clear to the next person who reads their code that they are  
> using the unsafe view-returning function.  
  
> It's also worth noting that a URL value-type using a std::string would  
> do no allocations anyway in this case, since "/index.htm" easily fits  
> into the string's SBO storage.  
  
> A good API should use std::string_view only for parameters, and  
> possibly for getter methods.  
  
> *I DON'T WANT THE POINTY ENDS FILED DOWN, I JUST WANT THEM TO HAVE  
> DANGER SIGNS ON THEM AND FOR THE TOOL TO ALSO BE USEABLE IN A SAFE WAY.*  
  
> Two of the C++Now videos that I've watched are Dave Abrahams on value  
> semantics in Swift and Val, and David Sankel on "Rust features that  
> I want in C++". Quote from the Swift talk: "C++ does have mutable  
> value semantics, in fact it's the default, it's just the no-one uses  
> it."  
>   
> It would be a disaster for C++ if it were to become only the language  
> to use if Rust or Swift or D or whatever doesn't do what you want.  
> C++ needs to learn from those languages. In particular, Dave Sankel's  
> talk has a lot about inclusiveness in Rust. Do please view it.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-08-22 16:22:07 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222592041  

> A good API should use std::string_view only for parameters, and  
> possibly for getter methods.  
  
Why would getter methods be OK?

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-08-22 16:50:02 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222628916  

Just had a look at [QUrl](https://doc.qt.io/qt-6/qurl.html) and the equivalent member functions would be something like  
  
```cpp  
std::string url_view_base::host(pct_decode_opts opts = {}) const;  
```

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-08-22 16:53:42 UTC  
> Updated at: 2022-08-22 17:29:26 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222633249  

> To me the problem is auto. In other words, the issue described here is not specific to URLs; it is general for any user that uses auto to capture the result of a function that returns a reference-like type over its arguments.  
  
Phil made a good point about `auto` and references vs. types acting as references, as references with `auto` are not ambiguous:  
  
```cpp  
std::string& get_string();  
std::string_view get_string_view();  
  
// ...  
  
auto s0 = get_string(); // value type  
auto& s1 = get_string(); // reference type  
auto s2 = get_string_view(); // acts as reference type  
auto& s3 = get_string_view(); // would act as reference to reference type but won't compile: cannot assign a temporary to a non-const reference  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-08-22 16:58:44 UTC  
> Updated at: 2022-08-22 16:58:55 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222640949  

That last line won't compile as you cannot assign a temporary to a non-const reference

---

## Comment 10

> Username: alandefreitas  
> Created at: 2022-08-22 17:29:37 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1222674178  

Yes. It's even worse.

---

## Comment 11

> Username: alandefreitas  
> Created at: 2022-08-22 21:16:44 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1223068021  

> One cool idea that I like is what abseil does in some cases, they return  
> you some "proxy" that morphs into what you specify as lhs. For example  
> absl::StrSplit()  
>   https://abseil.io/docs/cpp/guides/strings  
>   
> Not sure if that design has some problems beside being auto hostile.  
>   
  
This looks a lot like what we already have though.

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-08-22 21:27:18 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1223093217  

> I see url_view not as a view but as a universal  
> type for cheaply constructing different containers storing URLs. Of course,  
> this approach is incompatible with the "almost always auto" philosophy,  
> which some people now promote to simply "always auto". My personal opinion  
> is that this AAA philosophy is harmful from the outset and it begs for  
> problems and bugs. If someone adapts the AAA one must accept the  
> responsibility for the problems like the one with dangling references.

---

## Comment 13

> Username: alandefreitas  
> Created at: 2022-08-22 21:33:45 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1223108304  

#400

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-09-11 23:52:06 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1243071644  

Yes this seems like an `auto` problem and not a function name problem

---

## Comment 15

> Username: alandefreitas  
> Created at: 2022-09-13 00:25:31 UTC  
> Updated at: 2022-09-13 17:29:14 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1244739950  

One function that could exist is:  
  
```cpp  
auto v = parse_uri("http://www.boost.org"); // v is url_view  
auto u = make_uri("http://www.boost.org"); // u is url  
```  
  
I don't know if this would satisfy the reviewer though.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2022-09-13 13:32:55 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1245420849  

What's the difference here? would we have both? or just the new one?

---

## Comment 17

> Username: alandefreitas  
> Created at: 2022-09-13 17:31:42 UTC  
> Url: https://github.com/boostorg/url/issues/435#issuecomment-1245724867  

We would have both, to construct url views and urls.   
  
The only purpose would be to satisfy the reviewer. I don't think I would use these functions myself.   
  
I'm not even sure this would really satisfy the reviewer, since they might say `parse_uri` is still "confusing". (I'm quoting. I don't find it confusing.)  
  
I'm just mentioning an alternative. But I think I would just wait for people to complain and propose ideas.
