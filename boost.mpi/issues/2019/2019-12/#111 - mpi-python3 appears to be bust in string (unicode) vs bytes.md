# #111 - mpi-python3 appears to be bust in string (unicode) vs bytes [Closed]

> Username: xnox  
> Created at: 2019-12-05 21:29:02 UTC  
> Updated at: 2020-06-22 10:31:00 UTC  
> Closed at: 2020-06-22 10:31:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/111  

```  
$ cat demo2.py   
# https://www.boost.org/doc/libs/1_67_0/doc/html/mpi/tutorial.html#mpi.python  
  
import boost.mpi as mpi  
  
if mpi.world.rank == 0:  
    mpi.world.send(1, 0, 'Hello')  
    msg = mpi.world.recv(1, 1)  
    print(msg, '!')  
else:  
    msg = mpi.world.recv(0, 0)  
    print((msg + ', '), end=' ')  
    mpi.world.send(0, 1, 'world')  
```  
  
```  
$ mpirun --oversubscribe --allow-run-as-root -np 2 python3 ./demo2.py  
Traceback (most recent call last):  
  File "./demo2.py", line 6, in <module>  
    mpi.world.send(1, 0, 'Hello')  
TypeError: Expecting an object of type str; got an object of type bytes instead  
```  
  
It seems like Python unicode string is encoded into bytes, instead of being processed as a Python unicode string?!

---

## Comment 1

> Username: xnox  
> Created at: 2020-06-08 06:13:38 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-640391654  

Hello, any update on this?

---

## Comment 2

> Username: aminiussi  
> Created at: 2020-06-08 06:31:37 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-640397532  

Not yet, I'll try to work on it this afternoon.  
  
Thanks for the update

---

## Comment 3

> Username: aminiussi  
> Created at: 2020-06-10 19:35:14 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642214400  

Ok, I just reproduced.

---

## Comment 4

> Username: aminiussi  
> Created at: 2020-06-10 22:35:33 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642303047  

It's not a matter of string. I can't get the test working with int either, and it's before reaching the c++ send code...

---

## Comment 5

> Username: xnox  
> Created at: 2020-06-11 00:50:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642341238  

> It's not a matter of string. I can't get the test working with int either, and it's before reaching the c++ send code...  
  
that can't be good

---

## Comment 6

> Username: aminiussi  
> Created at: 2020-06-11 03:54:12 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642390330  

it could be a problem with my environment though.

---

## Comment 7

> Username: aminiussi  
> Created at: 2020-06-11 08:44:36 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642504446  

it could be a problem with my environment though.

---

## Comment 8

> Username: aminiussi  
> Created at: 2020-06-11 08:48:25 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642506255  

Ok, I'm got some "expected" problem. The point I mention above was due to the activation of my "production" boost distribution.

---

## Comment 9

> Username: aminiussi  
> Created at: 2020-06-11 08:49:23 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642506709  

Still, it's the first time I look into this area (boost mpi python bindings) so there is a learning curve.

---

## Comment 10

> Username: aminiussi  
> Created at: 2020-06-11 09:32:38 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642531949  

I'm starting to wonder if python3 support was even implemented.

---

## Comment 11

> Username: aminiussi  
> Created at: 2020-06-11 09:54:05 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642542031  

Or maybe it's related with the fact that we now assume uniform cluster on default, and serialize the data in binary format in that case. This error occurs in pickle and such messages have been mentioned in that area.

---

## Comment 12

> Username: aminiussi  
> Created at: 2020-06-11 10:39:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642562579  

I suspect it's related to: https://stackoverflow.com/questions/23582489/python-pickle-protocol-choice  
  
Although it default to -1 in our case.

---

## Comment 13

> Username: aminiussi  
> Created at: 2020-06-11 10:43:31 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642564412  

Or maybe:  
  
```  
str pickle::dumps(object obj, int protocol)  
{  
  if (!data) initialize_data();  
  return extract<str>((data->dumps)(obj, protocol));  
}  
```  
Which would only work with older python, since `str` does not look compatible with newer protocol which supports bytes.

---

## Comment 14

> Username: aminiussi  
> Created at: 2020-06-11 11:00:41 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642571437  

```  
[alainm@castor libs]$ python3  
Python 3.7.4 (default, Nov 22 2019, 21:31:39)   
[GCC 7.3.0] :: Intel(R) Corporation on linux  
Type "help", "copyright", "credits" or "license" for more information.  
Intel(R) Distribution for Python is brought to you by Intel Corporation.  
Please check out: https://software.intel.com/en-us/python-distibution  
>>> import pickle  
>>> pickle.dumps(42)  
b'\x80\x03K*.'  
>>> pickle.HIGHEST_PROTOCOL  
4  
>>> ^D  
[alainm@castor libs]$ python2  
Python 2.7.5 (default, Jul 13 2018, 13:06:57)   
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux2  
Type "help", "copyright", "credits" or "license" for more information.  
>>> import pickle  
>>> pickle.dumps(42)  
'I42\n.'  
>>> pickle.HIGHEST_PROTOCOL  
2  
>>>   
[alainm@castor libs]$  
```  
Probably never worked in python3....

---

## Comment 15

> Username: aminiussi  
> Created at: 2020-06-11 20:29:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642912353  

As for a start, in hotfix/pickle_py3_friendly_api, use object as buffer type.  
Str is python2 specific, we need to deal with python3's bytes

---

## Comment 16

> Username: xnox  
> Created at: 2020-06-12 00:21:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-642994182  

> As for a start, in hotfix/pickle_py3_friendly_api, use object as buffer type.  
> Str is python2 specific, we need to deal with python3's bytes  
  
Pickled data is always binary, thus one can treat it as always bytes, in both python2 and python3.  
  
Note that in python2 `byte == str` i.e. they are the same type. And just `byte` in python3.  
  
The special class for text was `unicode` in python2 and simply `str` in python3.

---

## Comment 17

> Username: aminiussi  
> Created at: 2020-06-18 14:25:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-646053629  

> Pickled data is always binary, thus one can treat it as always bytes, in both python2 and python3.  
  
python2 clearly encode it as a str object on default, although the protocol may chose a specific serialization technique.  
  
Anyway, I'm starting the see the light (I hate debugging that section), should have something to test soon.

---

## Comment 18

> Username: xnox  
> Created at: 2020-06-18 14:28:19 UTC  
> Updated at: 2020-06-18 14:28:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-646055059  

You say str object, but such thing does not exist in python2, it's bytes.  
  
anyway, when you having something to test/review, I'd gladly will do code review & testing.

---

## Comment 19

> Username: aminiussi  
> Created at: 2020-06-18 14:35:49 UTC  
> Updated at: 2020-06-18 14:37:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-646059531  

I have str on 2.7 and bites on 3.7:  
```  
alainm@jarvis:~$ python2.7  
Python 2.7.17 (default, Apr 15 2020, 17:20:14)   
[GCC 7.5.0] on linux2  
Type "help", "copyright", "credits" or "license" for more information.  
>>> import pickle  
>>> pickle.dumps(42)  
'I42\n.'  
>>> type(pickle.dumps(42))  
<type 'str'>  
>>>   
alainm@jarvis:~$ python3.6  
Python 3.6.9 (default, Apr 18 2020, 01:56:04)   
[GCC 8.4.0] on linux  
Type "help", "copyright", "credits" or "license" for more information.  
>>> import pickle  
>>> pickle.dumps(42)  
b'\x80\x03K*.'  
>>> type(pickle.dumps(42))  
<class 'bytes'>  
>>>   
alainm@jarvis:~$   
```  
> anyway, when you having something to test/review, I'd gladly will do code review & testing.  
  
Thanks

---

## Comment 20

> Username: aminiussi  
> Created at: 2020-06-18 15:03:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-646078759  

Hi,   
  
If you can have a look at branch `hotfix/pickle_py3_friendly_api` ?  
  
I still need to check that is still work with python 2 and then on my linux box.  
  
It's been tested with Intel 2020 compiler,MPI,python3.7.4

---

## Comment 21

> Username: xnox  
> Created at: 2020-06-18 17:04:50 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-646178814  

```  
$ python2.7 -c 'import pickle; print(isinstance(pickle.dumps(42),bytes))'  
True  
$ python3.8 -c 'import pickle; print(isinstance(pickle.dumps(42),bytes))'  
True  
```  
  
Is what I mean.

---

## Comment 22

> Username: aminiussi  
> Created at: 2020-06-22 10:30:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-647431379  

Merged #121   
  
Still need to fix the module name issue

---

## Comment 23

> Username: aminiussi  
> Created at: 2020-06-22 10:31:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/111#issuecomment-647431621  

Buut that is another issue so we can consider trhis one closed
