# #53 - misspelled macro name in docs [Open]

> Username: jefftrull  
> Created at: 2020-09-04 05:28:40 UTC  
> Updated at: 2021-03-10 07:15:25 UTC  
> Url: https://github.com/boostorg/chrono/issues/53  

`chrono.qbk` and `reference.html` refer to `BOOST_HAS_CLOCK_STEADY` but I think it's meant to be `BOOST_CHRONO_HAS_CLOCK_STEADY`. The former is not defined anywhere.

---

## Comment 1

> Username: jefftrull  
> Created at: 2021-03-10 07:15:25 UTC  
> Url: https://github.com/boostorg/chrono/issues/53#issuecomment-795004902  

That's https://github.com/boostorg/chrono/blob/aa51cbd5121ed29093484f53e5f96e13a9a915b4/doc/chrono.qbk#L3835
