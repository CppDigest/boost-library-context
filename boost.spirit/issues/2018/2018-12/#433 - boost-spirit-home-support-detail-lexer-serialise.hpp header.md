# #433 - boost/spirit/home/support/detail/lexer/serialise.hpp header [Closed]

> Username: Kojoley  
> Created at: 2018-12-29 23:08:17 UTC  
> Updated at: 2018-12-30 14:00:46 UTC  
> Closed at: 2018-12-30 14:00:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/433  

There is a strange header [`boost/spirit/home/support/detail/lexer/serialise.hpp`](https://github.com/boostorg/spirit/blob/c87e8772b4223fe0b8e7ed6751d1a91ae65240fe/include/boost/spirit/home/support/detail/lexer/serialise.hpp), it defines a serialization function for lex state machine class, but the header is not used anywhere in Spirit. The location under `detail` folder makes it explicitly prohibited to be used outside of the Spirit itself (but I still searched Boost and Internet and did not find anything). There is unneeded Boost.Serialization include in it that is being found by `boostdep` utility marking Spirit as having a dependency on it (there is even an entry on the Boost wiki about this https://svn.boost.org/trac10/wiki/ModuleDepednecies#Spirit-Serialization), but I cannot even test its removal because there are no tests or usages. All this makes me think that it would be better to remove the header completely.

---

## Comment 1

> Username: hkaiser  
> Created at: 2018-12-29 23:21:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/433#issuecomment-450527847  

I think this is an artifact of wave using this lexer with certain configurations. Wave also optionally allows for serializing its state, so I assume this serialization code ended up in Spirit... If we remove it from spirit we should add it to wave in order not to break anything there.

---

## Comment 2

> Username: hkaiser  
> Created at: 2018-12-29 23:26:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/433#issuecomment-450528127  

I have to take back what I said. There is no mention of `serialise.hpp` in wave. Looks like this header is an artifact of some experiments.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-12-29 23:29:51 UTC  
> Updated at: 2018-12-29 23:39:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/433#issuecomment-450528315  

Actually there is a trace in Wave but no usages https://github.com/boostorg/wave/blob/24f714a9c59fb2acfcb1e25bce73454d8dadea92/samples/list_includes/lexertl/lexertl_lexer.hpp#L39-L41, There is also a mail on the mail list from you about `serialise.hpp` https://lists.boost.org/Archives/boost/2007/11/130745.php but I cannot find any other messages of the thread, even searching the quoted text gives no results.

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-12-29 23:57:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/433#issuecomment-450529471  

However, the commentouted code in Wave examples is not a big reason to maintain the header. What are your suggestions @hkaiser?

---

## Comment 5

> Username: hkaiser  
> Created at: 2018-12-30 02:38:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/433#issuecomment-450535200  

I agree, no need to keep the file.
