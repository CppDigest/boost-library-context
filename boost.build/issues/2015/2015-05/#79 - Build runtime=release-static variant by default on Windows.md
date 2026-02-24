# #79 - Build runtime=release-static variant by default on Windows [Open]

> Username: OlafvdSpek  
> Created at: 2015-05-05 14:06:39 UTC  
> Updated at: 2015-05-21 07:46:44 UTC  
> Url: https://github.com/boostorg/build/issues/79  

Could the variant with static release runtime be build by default?  
  
It's a frequently used option to allow xcopy deployments.

---

## Comment 1

> Username: vprus  
> Created at: 2015-05-05 17:08:38 UTC  
> Url: https://github.com/boostorg/build/issues/79#issuecomment-99143380  

Is there a demonstrated consensus over at mailing lists to build that one by default?

---

## Comment 2

> Username: OlafvdSpek  
> Created at: 2015-05-05 17:10:11 UTC  
> Url: https://github.com/boostorg/build/issues/79#issuecomment-99143749  

No(t yet). Is one necessary?

---

## Comment 3

> Username: vprus  
> Created at: 2015-05-05 17:13:33 UTC  
> Url: https://github.com/boostorg/build/issues/79#issuecomment-99144352  

I'm afraid so. Adding another variable to the matrix has significant cost for every user, so I'd rather not do that unless we're sure that a significant percentage will be happy.

---

## Comment 4

> Username: OlafvdSpek  
> Created at: 2015-05-13 12:24:52 UTC  
> Url: https://github.com/boostorg/build/issues/79#issuecomment-101648250  

Right. Perhaps a note could be added to the getting started guide then.

---

## Comment 5

> Username: vprus  
> Created at: 2015-05-21 07:46:44 UTC  
> Url: https://github.com/boostorg/build/issues/79#issuecomment-104168752  

Yeah, though it appears that getting started guide no longer builds - with a problem somewhere between docutils and Python. Will tweak the wording when I get past that hurdle.
