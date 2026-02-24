# #133 - Issue in regex pattern [Closed]

> Username: thebatvic  
> Created at: 2021-06-04 08:35:10 UTC  
> Updated at: 2021-10-09 18:51:16 UTC  
> Closed at: 2021-10-09 18:51:16 UTC  
> Url: https://github.com/boostorg/regex/issues/133  

I'm using boost regex 1.73.0  
  
The regex statement with pattern and regex_constants using is  
`boost::regex rgx = boost::regex("^\\s*auth\\s+(?:required|requisite)\\s+pam_wheel\\.so(?:\\s+[^#]+)?(?:\\s+(?!\\2)(use_uid\\b|group=\\S+\\b))(?:\\s+[^#]+)?(?:\\s+(?!\\1)(use_uid\\b|group=\\S+\\b))", boost::regex_constants::perl | boost::regex_constants::icase | boost::regex_constants::no_mod_s);`  
  
The error i'm getting is   
`terminate called after throwing an instance of 'boost::wrapexcept<boost::regex_error>'  
  what():  Invalid back reference: specified capturing group does not exist.  The error occurred while parsing the regular expression fragment: '?(?:\s+(?!>>>HERE>>>\2)(use_ui'.`  
    
If this error is due to forward reference, I want to know whether regex does support forward reference or not?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-06-06 07:57:54 UTC  
> Url: https://github.com/boostorg/regex/issues/133#issuecomment-855356584  

Not like that, did you mean to use a recursion?  At the point that \2 is seen, it's impossible for the second sub-expression (the final `(use_uid\\b|group=\\S+\\b))`) to have already been matched at that point.  Remember that back-references match the literal text that a sub-expression matched, where as recursions "call" the sub-expression.

---

## Comment 2

> Username: thebatvic  
> Created at: 2021-07-05 12:26:26 UTC  
> Url: https://github.com/boostorg/regex/issues/133#issuecomment-874073449  

> Not like that, did you mean to use a recursion? At the point that \2 is seen, it's impossible for the second sub-expression (the final `(use_uid\\b|group=\\S+\\b))`) to have already been matched at that point. Remember that back-references match the literal text that a sub-expression matched, where as recursions "call" the sub-expression.  
  
No I'm not using any recursion, whenever a reference is made before the capturing group appears is called forward reference.  
It is supported in perl, In boost also I'm using perl flag only, but it shows the above mentioned error.  
Also in this [page](https://www.regular-expressions.info/backref2.html), they have mentioned boost regex does not support forward reference.  
Is that true? If yes, what is the use of perl flag in regex?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-07-05 18:45:29 UTC  
> Url: https://github.com/boostorg/regex/issues/133#issuecomment-874278772  

OK, I see the issue.  At some point I went to some considerable lengths to trap this as an error, so probably Perl has changed behaviour at some point and we haven't caught up yet.  There is no workaround currently, but I'll fix this eventually.

---

## Comment 4

> Username: thebatvic  
> Created at: 2021-07-06 07:27:45 UTC  
> Url: https://github.com/boostorg/regex/issues/133#issuecomment-874527651  

Thanks for considering this.  
Is there any where you guys can track these issues in github?, so that I too can know the status of this issue.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-07-06 07:50:45 UTC  
> Url: https://github.com/boostorg/regex/issues/133#issuecomment-874541424  

It's tracked right here.

---

## Comment 6

> Username: thebatvic  
> Created at: 2021-07-06 08:08:32 UTC  
> Updated at: 2021-07-06 08:08:51 UTC  
> Url: https://github.com/boostorg/regex/issues/133#issuecomment-874553092  

> It's tracked right here.  
  
Oh, thanks for the info.
