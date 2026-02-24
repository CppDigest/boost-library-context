# #305 - boost test documentation tells users to file bugs with decommissioned Trac. [Closed]

> Username: jonesmz  
> Created at: 2021-03-18 19:49:17 UTC  
> Updated at: 2022-03-08 18:12:00 UTC  
> Closed at: 2022-03-08 17:53:00 UTC  
> Url: https://github.com/boostorg/test/issues/305  

https://www.boost.org/doc/libs/1_75_0/libs/test/doc/html/boost_test/section_faq.html  
```  
I found a bug. Where can I report it?  
  
You can send a bug report to the boost users' mailing list and/or fill a ticket here https://svn.boost.org/trac/boost/.   
```

---

## Comment 1

> Username: mclow  
> Created at: 2021-03-18 20:46:06 UTC  
> Updated at: 2021-03-18 20:46:17 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-802284764  

The boost-users mailing list is at boost-users@lists.boost.org

---

## Comment 2

> Username: jonesmz  
> Created at: 2021-06-10 17:51:51 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-858838808  

That's not the problem...  
  
The problem is that the documentation for the library is telling users to make bug reports on a bug tracker that is no longer used.

---

## Comment 3

> Username: rdoeffinger  
> Created at: 2022-02-01 09:17:39 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-1026626711  

That documentation is actually part of this repo: https://github.com/boostorg/test/blob/develop/doc/closing_chapters/faq.qbk  
Maybe a pull request would get it addressed faster?

---

## Comment 4

> Username: jonesmz  
> Created at: 2022-02-13 22:56:02 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-1038464353  

But what should the change actually be? File bugs on github?

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2022-03-08 17:53:00 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-1062046574  

Merged in https://github.com/boostorg/test/pull/337 rev https://github.com/boostorg/test/commit/b52b0ea6429c393485f8ea27ab5edd678823e65a.

---

## Comment 6

> Username: rdoeffinger  
> Created at: 2022-03-08 17:57:38 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-1062051317  

There seems to be a typo in the merged README.md, it says BOOST_TEXT_CASE where it should be BOOST_TEST_CASE I think?

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2022-03-08 18:12:00 UTC  
> Url: https://github.com/boostorg/test/issues/305#issuecomment-1062064301  

Good catch! fixed in develop.
