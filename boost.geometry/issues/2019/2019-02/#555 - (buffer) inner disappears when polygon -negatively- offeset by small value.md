# #555 - (buffer) inner disappears when polygon *negatively* offeset by small value [Closed]

> Username: henzim  
> Created at: 2019-02-19 10:50:15 UTC  
> Updated at: 2019-03-25 10:19:53 UTC  
> Closed at: 2019-03-25 10:19:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/555  

I have a polygon with a single inner, which is well separated from the outer.  
When offsetting it by -1e-6 the inner disappears.   
(A small positive offset works)  
  
Original:  
![image](https://user-images.githubusercontent.com/31598216/53009687-588f9e00-343c-11e9-8491-b0ca7a79222b.png)  
Offset:  
![image](https://user-images.githubusercontent.com/31598216/53009718-66ddba00-343c-11e9-87ff-180620a1ccea.png)  
  
Wandbox:  
https://wandbox.org/permlink/YAoahmzv962aUTOS

---

## Comment 1

> Username: henzim  
> Created at: 2019-02-19 10:58:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/555#issuecomment-465085560  

It seems to somehow be related to the sign of the distance used in the buffering.   
Reversing the inner and setting it as outer of a new polygon and offsetting this polygon *outwards* works.  
  
This will have to do as a workaround for now...

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-02-19 18:16:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/555#issuecomment-465248304  

Thanks for the report. If I call it with  -0.00001 instead, (and apply geometry::correct) then in works. But indeed it does not work with the small value you provide.  
  
I will take a look at it later.

---

## Comment 3

> Username: henzim  
> Created at: 2019-02-20 08:04:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/555#issuecomment-465467195  

Thanks for your time!  
It seems to depend on the polygon, for some, also smaller values work without a problem.  
  
Your comment made me think that I might not use geometry::correct correctly.   
Can you please elaborate on the parenthesis in your comment; Should one always perform a geometry::correct after buffering?  
I tried performing one before, but that also did not help in this case.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2019-02-20 17:34:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/555#issuecomment-465679026  

No, geometry::correct should not be necessary after buffering.  
  
I called it before because I did not manually check if the input was correct. So before calling geometry::buffer, you could call geometry::correct. But if you're sure the input is correct, you don't have to do that.  
  
And indeed, it did not help in this case.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2019-03-02 16:28:43 UTC  
> Updated at: 2019-03-02 16:30:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/555#issuecomment-468935714  

I looked further today. The bad news is that it is not yet solved with the default settings. But the good news is that it is solved if you define BOOST_GEOMETRY_NO_ROBUSTNESS. Then internal rescaling is turned off, and the operation is successful. I added your case to the testsuite.  
  
Of course - I have to add that **all** your testcases will then be called without rescaling. But this will probably be OK.  
  
Another part of the good news is that rescaling will be turned off by default, hopefully in one or two versions from now on (so not yet the coming version).  
  
Therefore effectively it is solved already. Is it OK if I close this ticket?

---

## Comment 6

> Username: henzim  
> Created at: 2019-03-25 10:19:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/555#issuecomment-476133409  

Thanks for your reply.  
In the past I have already played around with BOOST_GEOMETRY_NO_ROBUSTNESS in order to avoid some problems. Unfortunately, I then ran into other, slightly different problems which made me turn it back on and try to work around the problems.   
This is in a sense not quite satisfactory, since with either setting I cannot guarantee that my workaround won't run in to one problem or the other.  
However, this is the first problem I have had in a while now, and I will define BOOST_GEOMETRY_NO_ROBUSTNESS again, and see what happens.  
  
Thanks for your time, you can close the ticket.
