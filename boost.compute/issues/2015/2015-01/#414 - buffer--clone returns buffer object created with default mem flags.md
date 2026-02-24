# #414 - buffer::clone returns buffer object created with default mem flags [Closed]

> Username: j8asic  
> Created at: 2015-01-17 18:20:46 UTC  
> Updated at: 2015-02-13 16:07:57 UTC  
> Closed at: 2015-02-13 16:07:53 UTC  
> Url: https://github.com/boostorg/compute/issues/414  

Shouldn't maybe buffer::clone respect and use same cl_mem_flags as buffer being cloned from?

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-01-21 04:06:30 UTC  
> Url: https://github.com/boostorg/compute/issues/414#issuecomment-70781526  

That makes sense to me, I was probably just lazy when writing that function. Would you be interested in implementing this change?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-02-07 20:28:36 UTC  
> Url: https://github.com/boostorg/compute/issues/414#issuecomment-73381495  

Implemented in PR #423.

---

## Comment 3

> Username: j8asic  
> Created at: 2015-02-13 10:00:54 UTC  
> Url: https://github.com/boostorg/compute/issues/414#issuecomment-74230455  

Thanks for the patch. Sorry, wasn't active on the project last month, else I would have written those few lines.

---

## Comment 4

> Username: kylelutz  
> Created at: 2015-02-13 16:07:49 UTC  
> Url: https://github.com/boostorg/compute/issues/414#issuecomment-74277652  

No problem. Thanks for the bug report!
