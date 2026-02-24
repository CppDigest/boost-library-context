# #703 - read_wkt() too strict on whitespace? [Closed]

> Username: dkondor  
> Created at: 2020-04-23 06:07:18 UTC  
> Updated at: 2023-01-04 09:58:12 UTC  
> Closed at: 2023-01-04 09:58:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/703  

This might be more of a feature request than a real issue. Any case, I'm deciding to report it here in case someone else runs into the same problem.  
  
Currently, read_wkt() throws an exception if the input contains a tab or newline character anywhere. This can be a hassle if the WKT is coming from a source which includes any of these.  
  
I understand this might be an intentional design decision. At the same time, not being an expert in the field, I did not easily find information about what kind of whitespace is allowed in WKT format.  
  
Would it be possible for read_wkt() to be more relaxed in this regard? (Either by default or with an optional parameter)  
  
If not, it would be really helpful if the documentation explicitly stated that only spaces are allowed. Also, the exception texts are hard to interpret, e.g. from the exception texts, it is hard to understand that the problem is with the wrong type of whitespace included in the string. It would be really helpful if the exception texts could mention that the problematic character is a tab or newline.  
  
I'm attaching a short example code with a few failing cases.  
[wkt_test.txt](https://github.com/boostorg/geometry/files/4520544/wkt_test.txt)

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-04-23 13:39:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-618400425  

> I understand this might be an intentional design decision.  
  
Yes, AFAIU the standard allows only space, see:  
https://www.ogc.org/standards/sfa  
page 54  
```  
<space>= " " // unicode "U+0020" (space)  
```  
  
Allowing tabs seems to be reasonable but newlines not so much. And there is also `\r` as well as all other unicode spaces. And this would be checked for all users, not only the ones who deal with non-conformant inputs. It could be an option, but this would make the interface unnecessarily more complex, even though slightly.  
  
Therefore currently I'm leaning towards leaving it as it is and forcing the user to prepare the correct input before passing it into `read_wkt()`, e.g. with Boost.StringAlgorithm's `replace()` or even better `erase()` to replace/remove all whitespaces.  
  
What is your opinion @mloskot @barendgehrels @vissarion ?

---

## Comment 2

> Username: awulkiew  
> Created at: 2020-04-23 13:39:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-618400555  

> I understand this might be an intentional design decision.  
  
Yes, AFAIU the standard allows only space, see:  
https://www.ogc.org/standards/sfa  
page 54  
```  
<space>= " " // unicode "U+0020" (space)  
```  
  
Allowing tabs seems to be reasonable but newlines not so much. And there is also `\r` as well as all other unicode spaces. And this would be checked for all users, not only the ones who deal with non-conformant inputs. It could be an option, but this would make the interface unnecessarily more complex, even though slightly.  
  
Therefore currently I'm leaning towards leaving it as it is and forcing the user to prepare the correct input before passing it into `read_wkt()`, e.g. with Boost.StringAlgorithm's `replace()` or even better `erase()` to replace/remove all whitespaces.  
  
What is your opinion @mloskot @barendgehrels @vissarion ?

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-04-23 13:40:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-618400838  

> I understand this might be an intentional design decision.  
  
Yes, AFAIU the standard allows only space, see:  
https://www.ogc.org/standards/sfa  
page 54  
```  
<space>= " " // unicode "U+0020" (space)  
```  
  
Allowing tabs seems to be reasonable but newlines not so much. And there is also `\r` as well as all other unicode spaces. And this would be checked for all users, not only the ones who deal with non-conformant inputs. It could be an option, but this would make the interface unnecessarily more complex, even though slightly.  
  
Therefore currently I'm leaning towards leaving it as it is and forcing the user to prepare the correct input before passing it into `read_wkt()`, e.g. with Boost.StringAlgorithm's `replace()` or even better `erase()` to replace/remove all whitespaces.  
  
What is your opinion @mloskot @barendgehrels @vissarion ?

---

## Comment 4

> Username: awulkiew  
> Created at: 2020-04-23 13:42:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-618401402  

> I understand this might be an intentional design decision.  
  
Yes, AFAIU the standard allows only space, see:  
https://www.ogc.org/standards/sfa  
page 54  
```  
<space>= " " // unicode "U+0020" (space)  
```  
  
Allowing tabs seems to be reasonable but newlines not so much. And there is also `\r` as well as all other unicode spaces. And this would be checked for all users, not only the ones who deal with non-conformant inputs. It could be an option, but this would make the interface unnecessarily more complex, even though slightly.  
  
Therefore currently I'm leaning towards leaving it as it is and forcing the user to prepare the correct input before passing it into `read_wkt()`, e.g. with Boost.StringAlgorithm's `replace()` or even better `erase()` to replace/remove all whitespaces.  
  
What is your opinion @mloskot @barendgehrels @vissarion ?

---

## Comment 5

> Username: vissarion  
> Created at: 2020-04-24 06:50:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-618835553  

I am in favour of supporting the standard and leaving the rest of string manipulation to the user.

---

## Comment 6

> Username: mloskot  
> Created at: 2020-04-24 12:59:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-618993218  

I agree with @vissarion   
  
For reference, here is what PostGIS reports for the examples in question  
  
```sql  
SELECT ST_GeomFromText('LINESTRING(1.0 2.0, \n 2.0 1.0)', 0); -- // fails  
-- 	ERROR:  parse error - invalid geometry  
-- 	HINT:  "LINESTRING(1.0 2.0, \n" <-- parse error at position 22 within geometry  
				     
SELECT ST_GeomFromText('\t\tLINESTRING(1.0 2.0, 2.0 1.0)', 0); -- // fails  
-- 	ERROR:  parse error - invalid geometry  
-- 	HINT:  "\t" <-- parse error at position 2 within geometry  
  
SELECT ST_GeomFromText('LINESTRING(1.0\t2.0, 2.0\t1.0)', 0); -- // fail  
-- ERROR:  parse error - invalid geometry  
-- HINT:  "LINESTRING(1.0\t" <-- parse error at position 16 within geometry  
```  
  
One difference between PostGIS and Boost.Geometry is this case:  
  
```sql  
SELECT ST_GeomFromText('LINESTRING(1.0 2.0, 2.0 1.0)\n', 0);  
```  
  
which succeeds by flux, simply PostGIS terminates parsing at `)`, so the trailing `\n` is just ignored.  
  
BTW, SQL Server is much more relaxed though and I wouldn't take it as a standard implementation.

---

## Comment 7

> Username: dkondor  
> Created at: 2020-04-27 03:49:31 UTC  
> Updated at: 2020-04-27 03:49:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-619697999  

Thank you, I understand your points.  
I feel it would be really helpful if:  
  - there was a note in the documentation about whitespace  
  - exception text mentioned the problem is with whitespace (at least in obvious cases of \t, \n, \r -- the case of fancy Unicode whitespace is probably much less common)  
  
I can create a pull request for both of these if that's OK with you.

---

## Comment 8

> Username: dkondor  
> Created at: 2020-04-27 03:58:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-619700084  

> ```sql  
> SELECT ST_GeomFromText('LINESTRING(1.0 2.0, 2.0 1.0)\n', 0);  
> ```  
  
Actually my original problem included a case like this. Of course, I could easily fix it once I realized that my string contained an extra newline. Still, at first, I was baffled by the exception I get.

---

## Comment 9

> Username: awulkiew  
> Created at: 2020-04-28 12:21:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-620570710  

@dkondor I think we're leaning more towards keeping the existing behavior of `read_wkt()`.  
  
> I can create a pull request for both of these if that's OK with you.  
  
Of course we're ok with you creating a PR. Especially the documentation should be easy to improve. I'm not sure about the exception since it already does inform about the parsing error and the place in the string where it occurred, right? I only skimmed through the code though so it's probable I'm missing something. So if you think you could improve it then go for it.  
  
Thanks for raising this issue btw!

---

## Comment 10

> Username: dkondor  
> Created at: 2020-04-28 14:44:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-620652001  

Great, I created the PRs for what I had in mind. My problem with the exception was that even though it quotes the problematic token, if that token is a whitespace character, it was not obvious to me. Since no one complained on this before, it might be just that I'm not typical in your userbase :)

---

## Comment 11

> Username: barendgehrels  
> Created at: 2022-12-27 13:14:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-1365893313  

I implemented #1106 when closing #705   
  
Just read the discussion now - I missed that earlier. Even though `\t` etc are not in the standard, it still makes sense to support it. We handle WKT in a more allowing way anyway, for example by supporting `BOX` and `SEGMENT`, adding rings, supporting `POLYGON()` on top of `POLYGON EMPTY` etc.  
  
And I think a specific exception message for this is too much. It's more convenient to just support it.

---

## Comment 12

> Username: barendgehrels  
> Created at: 2023-01-04 09:58:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/703#issuecomment-1370713546  

Closing as implemented by #1106
