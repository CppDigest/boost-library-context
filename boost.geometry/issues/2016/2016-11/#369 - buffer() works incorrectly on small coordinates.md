# #369 - buffer() works incorrectly on small coordinates [Closed]

> Username: mmatrosov  
> Created at: 2016-11-23 13:36:51 UTC  
> Updated at: 2019-03-02 20:11:27 UTC  
> Closed at: 2019-03-02 18:16:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/369  

See the demo: http://melpon.org/wandbox/permlink/yMk39ZquRHSIZmKZ  
  
I'm dilating (buffering with positive distance) a small triangle, then convert both input and output polygons to linestrings and measure distance between them. Instead of expected distance I got zero. Output polygon has quite strange shape.   
  
The demo produces WKT for input and output shapes:  
  
    LINESTRING(-0.0149623 -0.0269555,-0.0149471 -0.0271355,-0.0149539 -0.0271028,-0.0149623 -0.0269555)  
    LINESTRING(-0.0138163 -0.0268588,-0.0138012 -0.0270389,-0.0160727 -0.0273712,-0.0160795 -0.0273385,-0.0161104 -0.0270204,-0.0138163 -0.0268588)  
  
That's how these shapes look on https://arthur-e.github.io/Wicket/sandbox-gmaps3.html:  
  
![image](https://cloud.githubusercontent.com/assets/3617951/20564534/b9d36d5e-b19e-11e6-809c-9d8afc674838.png)  
  
Thin vertical bar in the middle is the input. The wide shape is the output. If I tweak input a little, e.g. remove the second vertex from the input polygon, result is correct:  
  
![image](https://cloud.githubusercontent.com/assets/3617951/20563734/e99132d2-b19a-11e6-8a01-701634824834.png)  
  
Thus I assume my method is correct. Sorry for the strange numbers, I took them from the data I'm currently working on to prepare a repro-case for you. Here is the image of what I get in my project:  
  
![image](https://cloud.githubusercontent.com/assets/3617951/20563427/a3b91b9a-b199-11e6-8150-5c6e2e7b7763.png)  
  
Look for two red outlines, other stuff is irrelevant. The bigger one is the dilated version of the smaller one. As you can see, it has incorrect shape on the left top side.

---

## Comment 1

> Username: mmatrosov  
> Created at: 2016-11-23 14:33:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/369#issuecomment-262528172  

I just discovered that if you scale all the coordinates and values by 1000, the issue is gone. Maybe the problem is in some absolute epsilon values?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2017-03-26 11:04:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/369#issuecomment-289272919  

We will this year remove rescaling internally from overlay and buffer, and I think that will fix this issue.  
Because of this, and because you have a workaround, and because I have not much time now, I have to leave it like this, sorry if this is inconvenient. We can leave the ticket open to check after removing rescaling.

---

## Comment 3

> Username: mmatrosov  
> Created at: 2017-03-27 18:55:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/369#issuecomment-289550238  

Glad you had a chance to review the issue! Good luck with your rework!

---

## Comment 4

> Username: barendgehrels  
> Created at: 2019-03-02 18:16:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/369#issuecomment-468945856  

Sorry for the (very delayed) follow up. I believe it is working correctly now, with and without rescaling.  
This testcase is added to the testsuite.  
Rescaling is still not gone from the released library, but much further and hopefully it will be released in one or two releases. But your testcase also works with rescaling (at least: now).

---

## Comment 5

> Username: mmatrosov  
> Created at: 2019-03-02 20:11:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/369#issuecomment-468955957  

Better later than never :)
