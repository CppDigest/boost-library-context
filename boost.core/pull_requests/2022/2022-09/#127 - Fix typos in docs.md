# #127 Fix typos in docs [Merged]

> Username: tocic  
> Created at: 2022-09-18 09:45:09 UTC  
> Updated at: 2022-09-19 09:53:00 UTC  
> Merged at: 2022-09-19 09:53:00 UTC  
> Closed at: 2022-09-19 09:53:00 UTC  
> Url: https://github.com/boostorg/core/pull/127  

Use "Acknowledgments" everywhere for consistency.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-09-18 11:08:43 UTC  
> Url: https://github.com/boostorg/core/pull/127#issuecomment-1250246222  

"Acknowledgement" is the [correct](https://dictionary.cambridge.org/dictionary/english/acknowledgement) spelling.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2022-09-18 11:11:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/127#pullrequestreview-1111526489  

📁 doc/scoped_enum.qbk

```diff
  48 | 
  49 |- These macros allows to use `future_errc` in almost all the cases as an scoped enum.
  49 |+ These macros allow using `future_errc` in almost all the cases as a scoped enum.
```

> Username: Lastique  
> Created_at: 2022-09-18 11:11:04 UTC  
> Url: https://github.com/boostorg/core/pull/127#discussion_r973702779  

"Allow to use" seems correct to me. Is it not? I'm not a native speaker.

> Username: tocic  
> Created_at: 2022-09-18 13:45:15 UTC  
> Updated_at: 2022-09-18 13:45:16 UTC  
> Url: https://github.com/boostorg/core/pull/127#discussion_r973723776  

Please see https://english.stackexchange.com/q/60271.


---

## Comment 3

> Username: tocic  
> Created_at: 2022-09-18 13:45:20 UTC  
> Updated_at: 2022-09-18 13:48:31 UTC  
> Url: https://github.com/boostorg/core/pull/127#issuecomment-1250312219  

> "Acknowledgement" is the [correct](https://dictionary.cambridge.org/dictionary/english/acknowledgement) spelling.  
  
See [SO](https://english.stackexchange.com/q/390960), [ngrams](https://books.google.com/ngrams/graph?content=acknowledgments%2Cacknowledgements&year_start=1800&year_end=2000&corpus=en-2012&smoothing=3) and [trends](https://trends.google.com/trends/explore?date=all&q=acknowledgments,acknowledgements). I chose the "Acknowledgments" version, because [searching through the repo for it](https://github.com/boostorg/core/search?q=%22Acknowledgments%22) yields 6 results versus 2 for "Acknowledgements". I don't insist on this spelling, but it's better to be consistent. I'll convert everything to "Acknowledgements" if you wish.

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-09-18 14:54:43 UTC  
> Url: https://github.com/boostorg/core/pull/127#issuecomment-1250325448  

On September 18, 2022 4:45:30 PM tocic ***@***.***> wrote:  
  
>> "Acknowledgement" is the   
>> [correct](https://dictionary.cambridge.org/dictionary/english/acknowledgement)   
>> spelling.  
>  
> See https://english.stackexchange.com/q/390960. I chose the   
> "Acknowledgments" version, because [searching through the repo for   
> it](https://github.com/boostorg/core/search?q=%22Acknowledgments%22) yields   
> 6 results versus 2 for "Acknowledgements". I don't insist on this spelling,   
> but it's better to be consistent. I'll convert everything to   
> "Acknowledgements" if you wish.  
  
My personal preference would be to keep the 'e', mostly because that's how   
I'm used to. Other maintainers and authors may have different preferences.   
If both spellings are correct then I don't mind keeping every instance as   
is. But if you want to convert everything to one spelling (with 'e') , I   
won't object either.

---
