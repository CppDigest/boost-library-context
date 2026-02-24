# #1243 - feature request: file_body::value_type::reset() with file_size argument [Open]

> Username: hyperblast  
> Created at: 2018-09-09 18:02:52 UTC  
> Updated at: 2018-12-21 15:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1243  

Hi, thank you for the great project.  
  
I'd like to propose simple feature, namely add `file_body::value_type::reset()` overload that takes `file_size` as an extra parameter.  
  
This would allow saving one syscall for the cases when file size is already known in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-09 19:02:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419737202  

hmm...that is an interesting idea! Please note, that the `file_body` interface is going to change. Putting the file handle in the message was not a great idea.

---

## Comment 2

> Username: hyperblast  
> Created at: 2018-09-10 07:17:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419812501  

So, what's an alternative to file handles? File names?  
I think consuming pre-opened handles is still a very useful feature.  
File names are subject to the various race-condition scenarios.  
File handles on contrary specify exact file to be returned.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-09-10 12:37:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419897399  

There will still be a file handle of course, and you'll be able to set it, but instead of putting the file handle in the message (which forces the message to be passed by non-const reference and causes massive headaches) the file handle will be in the "BodyWriter" instance which is created by the serializer.  
  
In this model, you create the serializer first and then you can access the BodyWriter to set the file handle or open the file (so you can handle any file system errors). This lets the message always be passed as a const ref, so you can send the same message twice and do other interesting things like cache it in a table or something.  
  
Instead of the file handle, the message body will simply contain a path as you noted.

---

## Comment 4

> Username: hyperblast  
> Created at: 2018-09-10 12:41:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419898574  

Sounds reasonable, thank you for explanation.

---

## Comment 5

> Username: hyperblast  
> Created at: 2018-09-10 12:52:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419901274  

Some crazy idea:  
  
File handles are typically duplicable (`dup`, `DuplicateHandle`, etc),  
so BodyWriter could make a copy of a handle stored in `file_body`.  
This would allow keeping body as const.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-09-10 14:10:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419926323  

> BodyWriter could make a copy of a handle  
  
Interesting idea but there is no standard way to duplicate a `FILE*`.

---

## Comment 7

> Username: hyperblast  
> Created at: 2018-09-10 15:04:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-419946329  

You're right, I missed that part.  
Also it seems that duplicated handle share file position with its origin, so it would not work at all.  
  
I think this feature request is no longer that useful with new design.  
Would `file_body` even have a precomputed file size if it becomes pure file name?  
If so what to do when file size changes over the time?  
Dealing with files is not that simple at all :sweat_smile:

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-09-10 20:14:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1243#issuecomment-420045018  

You bring up a good point and one that I hadn't thought of... this needs more thought.
