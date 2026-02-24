# #443 - Doc: How Do I Parse a URL? [Closed]

> Username: alandefreitas  
> Created at: 2022-08-22 06:10:32 UTC  
> Updated at: 2022-10-26 21:47:18 UTC  
> Closed at: 2022-10-26 21:47:18 UTC  
> Url: https://github.com/boostorg/url/issues/443  

> When I call parse_uri(), I get a result of type result<url_view>?  Why  
> isn't the function spelled parse_url(), or the return type spelled  
> result<uri_view>?  This seems needlessly inconsistent, given the  
> assertion in the docs that URL is fine to use in place of URI.   
  
It seems like no matter how we reiterate this in the docs, 3 out of 4 people are going to keep asking this. And 3 out of 4 people who didn't ask is because not using the `parse_*` and are constructing the urls directly with the constructor.  
  
In a way, we could say we are not necessarily following the convention we established on the docs, because we only really use all rule names in the `grammar` rules.   
  
The `parse_*` functions, on the other hand, are fewer: `parse_uri` (and variants), `parse_authority`, and `parse_path`. Unlike the rules in `grammar`, these are more focused on use cases than grammar rules. So `parse_url` and its variants doesn't really seem like a problem, and it avoids these recurrent questions.   
  
At this point, I guess it would be OK even if that were an exception to the general rule. Most people are not going to read the RFC, and tt seems like this minimizes the surprises to almost everyone.

---

## Comment 1

> Username: sehe  
> Created at: 2022-08-22 12:16:39 UTC  
> Url: https://github.com/boostorg/url/issues/443#issuecomment-1222275738  

Just replicating my review comment on the topic:  
  
> In contrast to others, I think the choice of Nomenclature, namely URL  
> everywhere _except_ in the grammar domain (the parse function names) is a very  
> elegant one: it avoids the historical naming swamp (by choosing the URL camp)  
> while still making it abundantly clear what parts of RFC are referenced.  
  
If really people are irked by the absense of the expectable `parse_url` can we remove that speedbump by adding `parse_url` as a documented alias for `parse_uri`? #bestofbothworlds

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-22 16:39:49 UTC  
> Updated at: 2022-08-22 16:41:01 UTC  
> Url: https://github.com/boostorg/url/issues/443#issuecomment-1222616186  

> If really people are irked by the absense of the expectable parse_url can we remove that speedbump by adding parse_url as a documented alias for parse_uri?  
  
LOL... that's how I had it originally :) But there's no grammar for a "URL". And after working with some people who were using the library I came to the conclusion that being _very_ precise and specific about what grammar is used in a parse is important.  
  
There could be a little pain for users at first when they discover 1. that there are actually different grammars, 2. that they need to know which grammar is correct for their use-case, and 3. which library function corresponds to that grammar. But I think this is a good thing because the result is concise code and a vocabulary that leaves no room for ambiguity.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-23 22:18:04 UTC  
> Url: https://github.com/boostorg/url/issues/443#issuecomment-1224946657  

I have nothing against `parse_url`  
  
99.999% of users expect that no one is going to read the RFC

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-23 23:23:13 UTC  
> Url: https://github.com/boostorg/url/issues/443#issuecomment-1224986751  

100% of Vinnies expect that users will be required to know what they are parsing

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-08-24 03:19:05 UTC  
> Url: https://github.com/boostorg/url/issues/443#issuecomment-1225132534  

> 100% of Vinnies expect that users will be required to know what they are parsing  
  
I'm fine with both. We could even make a bot to answer any issue including the term `parse_url`.  
  
We could wait for the review to finish and close this just in case. Is the review officially over? Did that extension happen? Anyway, it's technically still going on until the final result is there.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-08-25 14:41:08 UTC  
> Url: https://github.com/boostorg/url/issues/443#issuecomment-1227354728  

The review is over, the results are in, and its up to us to determine how to address this. I'm in favor of adding a doc heading (*not* a section) titled "How Do I Parse a URL?" and there we can explain that grammar matters, and most people will be satisfied with `parse_uri` (requires a scheme) or `parse_uri_reference` (used by the constructors).
