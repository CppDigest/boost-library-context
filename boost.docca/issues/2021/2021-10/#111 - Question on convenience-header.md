# #111 - Question on convenience-header [Closed]

> Username: vinniefalco  
> Created at: 2021-10-31 19:28:04 UTC  
> Updated at: 2021-10-31 21:12:34 UTC  
> Closed at: 2021-10-31 20:50:59 UTC  
> Url: https://github.com/boostorg/docca/issues/111  

How would I accomplish something like this?  
```  
  <xsl:template mode="convenience-header" match="@file[contains(., 'boost/url/bnf')]">url/bnf.hpp</xsl:template>  
  <xsl:template mode="convenience-header" match="@file[contains(., 'boost/url')]">url.hpp</xsl:template>  
  <xsl:template mode="convenience-header" match="@file"/>  
```  
  
I am getting "Ambiguous rule match." I want everything in url/bnf to use the bnf.hpp file, and everything else to use url.hpp.

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-10-31 20:42:32 UTC  
> Url: https://github.com/boostorg/docca/issues/111#issuecomment-955788276  

Add priority=“1” to the first rule. Otherwise they have the same default  
priority of 0.5.  
  
On Sun, Oct 31, 2021 at 12:28 PM Vinnie Falco ***@***.***>  
wrote:  
  
> How would I accomplish something like this?  
>  
>   <xsl:template mode="convenience-header" ***@***.***[contains(., 'boost/url/bnf')]">url/bnf.hpp</xsl:template>  
>   <xsl:template mode="convenience-header" ***@***.***[contains(., 'boost/url')]">url.hpp</xsl:template>  
>   <xsl:template mode="convenience-header" ***@***.***"/>  
>  
> I am getting "Ambiguous rule match." I want everything in url/bnf to use  
> the bnf.hpp file, and everything else to use url.hpp.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/docca/issues/111>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAETWLRINYUPZ6MSRV6HKH3UJWKE7ANCNFSM5HCT45ZQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-10-31 20:50:59 UTC  
> Url: https://github.com/boostorg/docca/issues/111#issuecomment-955789419  

Yep!!!

---

## Comment 3

> Username: evanlenz  
> Created at: 2021-10-31 20:51:21 UTC  
> Url: https://github.com/boostorg/docca/issues/111#issuecomment-955789462  

Also, let me know if that doesn’t work and I’ll take a closer look when I’m  
near my computer.  
  
On Sun, Oct 31, 2021 at 1:42 PM Evan Lenz ***@***.***> wrote:  
  
> Add priority=“1” to the first rule. Otherwise they have the same default  
> priority of 0.5.  
>  
> On Sun, Oct 31, 2021 at 12:28 PM Vinnie Falco ***@***.***>  
> wrote:  
>  
>> How would I accomplish something like this?  
>>  
>>   <xsl:template mode="convenience-header" ***@***.***[contains(., 'boost/url/bnf')]">url/bnf.hpp</xsl:template>  
>>   <xsl:template mode="convenience-header" ***@***.***[contains(., 'boost/url')]">url.hpp</xsl:template>  
>>   <xsl:template mode="convenience-header" ***@***.***"/>  
>>  
>> I am getting "Ambiguous rule match." I want everything in url/bnf to use  
>> the bnf.hpp file, and everything else to use url.hpp.  
>>  
>> —  
>> You are receiving this because you are subscribed to this thread.  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/docca/issues/111>, or unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/AAETWLRINYUPZ6MSRV6HKH3UJWKE7ANCNFSM5HCT45ZQ>  
>> .  
>> Triage notifications on the go with GitHub Mobile for iOS  
>> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
>> or Android  
>> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>>  
>>  
>

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-10-31 20:52:57 UTC  
> Url: https://github.com/boostorg/docca/issues/111#issuecomment-955789688  

It works (or else I would not have closed the issue) thanks!!!

---

## Comment 5

> Username: evanlenz  
> Created at: 2021-10-31 21:12:34 UTC  
> Url: https://github.com/boostorg/docca/issues/111#issuecomment-955792359  

Haha, yes, I emailed before I saw that. Glad to hear it!  
  
On Sun, Oct 31, 2021 at 1:53 PM Vinnie Falco ***@***.***>  
wrote:  
  
> It works (or else I would not have closed the issue) thanks!!!  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/docca/issues/111#issuecomment-955789688>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAETWLQRSM7TPVRYLXV5PO3UJWUDHANCNFSM5HCT45ZQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
>
