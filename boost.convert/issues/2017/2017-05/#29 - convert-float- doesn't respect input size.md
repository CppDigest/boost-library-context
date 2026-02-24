# #29 - convert<float> doesn't respect input size [Closed]

> Username: OlafvdSpek  
> Created at: 2017-05-15 08:43:31 UTC  
> Updated at: 2017-06-12 21:36:48 UTC  
> Closed at: 2017-06-12 21:36:48 UTC  
> Url: https://github.com/boostorg/convert/issues/29  

```  
const char* a = "42";  
boost::string_ref b(a, 1);  
float c = boost::convert<float>(b, boost::cnv::strtol(), -1);  
assert(c == 4);  
```

---

## Comment 1

> Username: yet-another-user  
> Created at: 2017-05-15 10:07:17 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-301432839  

char const* a = "42";  
float c = convert<float>(a, boost::cnv::strtol(), -1);  
printf("AA=%f\n", c);  
  
prints "AA=42.000000". I am not sure of boost::string_ref. Never used it...  
Did not even know such a thing existed. :-) Do you think I should learn,  
investigate and make convert work with it? I read it's being  
deprecated/repaced with string_view.  
  
  
On Mon, May 15, 2017 at 6:43 PM, Olaf van der Spek <notifications@github.com  
> wrote:  
  
>       const char* a = "42";  
>       boost::string_ref b(a, 1);  
>       float c = boost::convert<float>(b, boost::cnv::strtol(), -1);  
>  
> c == 42 ?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/29>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswIJPBWfKsfy5EoYwxZtEUdlXCS-LGks5r6BAzgaJpZM4Na1K5>  
> .  
>

---

## Comment 2

> Username: OlafvdSpek  
> Created at: 2017-05-15 10:08:33 UTC  
> Updated at: 2017-05-15 10:08:44 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-301433124  

Feel free to use string_view instead.

---

## Comment 3

> Username: yet-another-user  
> Created at: 2017-05-15 10:09:54 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-301433406  

OK. Thanks for the input. I'll look into it.

---

## Comment 4

> Username: OlafvdSpek  
> Created at: 2017-06-07 09:47:32 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-306746300  

Any luck?

---

## Comment 5

> Username: yet-another-user  
> Created at: 2017-06-07 11:17:12 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-306765036  

I am really sorry. I have not forgotten about it and It's been on my todo  
list. It is just that at work management is breathing down my neck for  
something important to deliver. The addition seems fairly straightforward.  
If you have some basics to implemented, I'd incorporate that gladly.  
Otherwise, I'll try to commit a weekend to it before the end of the month.  
  
  
On Wed, Jun 7, 2017 at 7:47 PM, Olaf van der Spek <notifications@github.com>  
wrote:  
  
> Any luck?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/convert/issues/29#issuecomment-306746300>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ABswICfedh3VXb2Rqf7Ydd1InGjHWPprks5sBnG0gaJpZM4Na1K5>  
> .  
>

---

## Comment 6

> Username: yet-another-user  
> Created at: 2017-06-08 00:33:02 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-306964352  

Looking into it... Indeed the str-to-double version of strtol-converter does not support ranges (unlike str-to-int)... because of the underlying std::strtold(). Now in order for the str-to-double version to support ranges I see the following options:  
  
1) replace std::strtold() with my own (what I did for str-to-int, feels like a bit of effort);  
2) incur the penalty of converting a range to, say, std::string and then applying the existing std::strtold();  
3) try and think of a reason/justification why   
  
        convert<double>(string_view("123.456", 2)...)  
  
   might not be a legitimate use-case. :-)   
  
If you do not mind me asking, what is your use-case where you needed to do the above conversion?.. Instead of, say, convert<int>... I am curious as I myself can't think of any. Tnx.

---

## Comment 7

> Username: OlafvdSpek  
> Created at: 2017-06-08 06:55:45 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307016128  

I've got {"latitude":52.319683777217,"longitude":4.9162592086987} and I want to extract the two numbers.. so I split the input (into string_view) and then use convert.

---

## Comment 8

> Username: yet-another-user  
> Created at: 2017-06-08 07:56:51 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307028563  

Tnx for the replay... What I am missing is that on one hand you want precision (you use convert\<float>) but on the other hand you do not want precision (you chop off quite a bit). In your case you might as well use convert\<int>, right?.. wrong? I am asking because I am not that eager having to re-implement std::strtold() (No.1) and No.2 does not look enticing either. So, I want to make sure your use-case is legit rather than capricious.  Sorry if that sounds somewhat blunt.

---

## Comment 9

> Username: OlafvdSpek  
> Created at: 2017-06-08 08:05:49 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307030539  

I'm not sure I understand your question. Are you saying I can't use float and I should use double instead?  
I need about 5 decimals so int doesn't cut it and double is too much.  
I understand re-implementing strtold isn't desirable though, perhaps ask for advice on the list?

---

## Comment 10

> Username: yet-another-user  
> Created at: 2017-06-08 08:27:47 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307035676  

I did not mean to say "use double". "double" and "float" use the same conversion function. I was only saying "double" out of habit. "float" behaves the same. So, if I understand correctly, in your case everything actually works -- convert\<float>("4.9162592086987"). This works just fine. No need to chop anything off, right? It is just that you (for some reason) want something like convert\<float>("4.9162") and no more. Right? If it is indeed so, do you mind me asking why mount all that (seemingly unnecessary) complexity?

---

## Comment 11

> Username: OlafvdSpek  
> Created at: 2017-06-08 08:34:29 UTC  
> Updated at: 2017-06-08 08:34:37 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307037317  

Ah... now I understand the confusion. I'm not chopping of anything from the number, it's due to parsing the input.  
  
```  
  string_view a = "{\"latitude\":52.319683777217,\"longitude\":4.9162592086987}";  
  string_view b = a.substr(12, 15); // 52.319683777217  
  float c = boost::convert<float>(b, boost::cnv::strtol(), 0); // c = 0  
```

---

## Comment 12

> Username: yet-another-user  
> Created at: 2017-06-08 08:50:51 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307041151  

Ah, now I understand. :-) Many thanks. Will fix fix it.

---

## Comment 13

> Username: yet-another-user  
> Created at: 2017-06-08 10:23:22 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307063264  

I've just checked in a fix/cheat that'll give you the correct result. The underlying str-to-double/float code still :-( does not recognize the input size (as it still uses std::strtold() directly on the input). However, it'll convert as many digits as possible, i.e. in "latitude\\":52.319683777217," it'll convert all digits to the comma... that's how std::strtold() works. That is, for str-to-float regardless if you supply a.substr(12, 15) or a.substr(12, 2) the result will be the same. However, it'll be an actual result but not a failure unlike before.  
  
I am still not sure if a) I want to copy the provided input length into an internal buffer (and pay penalty) or b) to convince myself :-) that it is highly unlikely for the str-to-double conversion to convert only "52.31" out of "52.319683777217"  and to pretend it'll never happen. I am leaning towards b).

---

## Comment 14

> Username: OlafvdSpek  
> Created at: 2017-06-08 10:42:15 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307067244  

Not recognizing input size might cause undefined behavior and access violations.. if the input is at the end of a page and the next page is invalid.

---

## Comment 15

> Username: yet-another-user  
> Created at: 2017-06-08 10:46:13 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307068057  

Yes, indeed... did not bloody occur to me that the range does not have to be 0-terminated. Alright then. Now I know what to do. Tnx.

---

## Comment 16

> Username: yet-another-user  
> Created at: 2017-06-09 01:29:01 UTC  
> Updated at: 2017-06-09 01:30:02 UTC  
> Url: https://github.com/boostorg/convert/issues/29#issuecomment-307270407  

Just checked in the fix. Added test:  
  
char const* const s = "1.23456";  
BOOST_TEST(1.2  == convert\<double>(string_view(s, 3)).value_or(0));  
BOOST_TEST(1.23 == convert\<double>(string_view(s, 4)).value_or(0));
