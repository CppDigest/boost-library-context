# #655 allow ascii symbols_parser to be matched against unicode chars [Closed]

> Username: theodelrieu  
> Created at: 2021-03-01 10:02:01 UTC  
> Updated at: 2025-05-09 11:51:30 UTC  
> Closed at: 2025-05-09 11:51:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/655  

@Kojoley   
  
Following up on #649, I inverted the test based on your comment. Let me know what you think about it!

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2021-03-01 20:10:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/655#pullrequestreview-601122965  

My gut feeling tells me that this will open a can of worms, consider this example: `BOOST_TEST((!test(U"\U000001613", no_case[foo])))`

---

## Comment 2

> Username: theodelrieu  
> Created_at: 2021-03-02 08:22:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/655#issuecomment-788715889  

@Kojoley True this can lead to weird stuff like your example.  
  
I'd like to avoid that kind of patches, but right now I don't see how to workaround my problem without writing all my grammars in UTF32.

---

## Comment 3

> Username: theodelrieu  
> Created_at: 2021-03-05 09:49:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/655#issuecomment-791305869  

@djowel What do you think about this one?

---

## Comment 4

> Username: djowel  
> Created_at: 2021-03-05 23:36:35 UTC  
> Updated_at: 2021-03-05 23:37:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/655#issuecomment-791790000  

I can understand @Kojoley's concern. I'm uneasy with it as well, but I do not have an easy solution ATM. Perhaps if you can start with a simple mvce use-case, we can find something that can work better. I'm thinking something explicit, rather than implicit.

---

## Comment 5

> Username: theodelrieu  
> Created_at: 2021-03-08 09:12:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/655#issuecomment-792603934  

Understood, I will try to come up with a MCVE later this month.

---

## Comment 6

> Username: saki7  
> Created_at: 2025-05-09 11:51:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/655#issuecomment-2866255233  

Closing, as this is an invalid use case involving a mix of ASCII and Unicode characters.  
ASCII and Unicode differ in fundamental ways and can’t simply be interchanged through a character-type-unaware interface.  
  
- See my comment for rationale: https://github.com/boostorg/spirit/issues/678#issuecomment-2376313379  
- Other reference: <https://tonsky.me/blog/unicode/>

---
