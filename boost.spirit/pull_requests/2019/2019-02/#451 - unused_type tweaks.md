# #451 unused_type tweaks [Closed]

> Username: Kojoley  
> Created at: 2019-02-03 21:22:20 UTC  
> Updated at: 2019-02-03 23:11:40 UTC  
> Closed at: 2019-02-03 23:11:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/451  

Added basic tests and on C++11 made the type trivial and all operations with it constexpr.

---

## Comment 1

> Username: cppljevans  
> Created_at: 2019-02-03 21:56:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/451#issuecomment-460092525  

Why make unused_type constexpr when just providing a constexpr unused_type::CTOR enables  
even non-const unused_type to be used in a constexpr expression?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-02-03 21:59:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/451#issuecomment-460092796  

Do you have any cons?

---

## Comment 3

> Username: cppljevans  
> Created_at: 2019-02-03 22:22:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/451#issuecomment-460094553  

On 2/3/19 3:59 PM, Nikita Kniazev wrote:  
 > Do you have any cons?  
 >  
Do you have a reason pro?  
  
IOW, if there's no reason for a more restrive type, then  
don't restrict the type to be more restrictive.  
It may be harmless, but it's redundant.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-02-03 22:31:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/451#issuecomment-460095212  

What it restricts you to do? You really expect any of those functions/methods to be changed?  
I will push X3 to be almost all constexpr so it will be possible to make parsers and parse text at compile time, the `unused_type` is a beginning as it used everywhere and I do not want to make it constexpr in several of iterations.

---

## Comment 5

> Username: cppljevans  
> Created_at: 2019-02-03 22:53:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/451#issuecomment-460096836  

On 2/3/19 4:31 PM, Nikita Kniazev wrote:  
> What it restricts you to do? You really expect any of those functions/methods to be changed?  
No.  Just as I wouldn't expect any functions/methods to change if  
unused declaration were changed to:  
  
   unused_type unused;  
  
where unused_type had a constexpr CTOR.  As I said, it may be harmless   
but redundant; hence, unnecessary.  
  
> I will push X3 to be almost all constexpr so it will be possible to make parsers and parse text at compile time, the `unused_type` is a beginning as it used everywhere and I do not want to make it constexpr in several of iterations.  
>   
According to compile tests I've done with declarations similar to the   
above `unused` declaration, this is possible:  
  
   struct tag_t { constexpr tag_t(){} };  
   tag_t tag_nc; //tag_nonconst  
   constexpr int result_nc=f(tag_nc);  
  
where, of course, tag_t is used in place of unused_type.  
  
Hence, I don't see how such a declaration would prevent you from  
accomplishing your goal to "make parsers and parse text at compile time".  
  
Please provide code examples actually supporting your contention.  
  
-regards,  
Larry

---

## Comment 6

> Username: Kojoley  
> Created_at: 2019-02-03 23:11:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/451#issuecomment-460098081  

I do not understand what you want to demonstrate with the example, it shows nothing.  
  
You still did not provide cons. What do you want from me, to close the PR? No problem, I do not want to have meaningless discussions about your fears. Every change I making to Spirit is hard for me and making it way to the repository even harder as I have to judge a lot of consequences. And when I receive 'worrying' like 'harmless  
but redundant; hence, unnecessary'.

---
