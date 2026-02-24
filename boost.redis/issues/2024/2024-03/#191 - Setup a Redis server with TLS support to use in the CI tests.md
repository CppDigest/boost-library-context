# #191 - Setup a Redis server with TLS support to use in the CI tests [Closed]

> Username: mzimbres  
> Created at: 2024-03-20 14:35:54 UTC  
> Updated at: 2024-04-03 14:02:56 UTC  
> Closed at: 2024-04-03 14:02:56 UTC  
> Url: https://github.com/boostorg/redis/issues/191  

I had to disable the TLS tests after [shutting down](https://github.com/boostorg/redis/blob/5c46b62958168bd746fd7560041498e105f70b32/test/CMakeLists.txt#L33) my server running on `occase.de`. A new one will be needed in the CI. I believe the work of @anarthal in Boost.MySql could offer some guidance here (although I haven't looked at it yet).

---

## Comment 1

> Username: anarthal  
> Created at: 2024-03-20 15:03:45 UTC  
> Url: https://github.com/boostorg/redis/issues/191#issuecomment-2009789846  

My recommendation is to:  
  
* Create a Docker image encapsulating the Redis server you want. This includes the SSL configuration. The image should be based on one of the official Redis Docker images, just adding the files you want. For instance, [this Dockerfile](https://github.com/boostorg/mysql/blob/develop/tools/docker/mysql5.dockerfile) creates a MySQL 5.x image. It's just the official image, plus a couple of well-known SSL certificates, a well-known password, etc. By doing this, your tests know for sure what to expect from the server.  
* Build your Docker images and store them in a registry you control. This is just invoking `docker build` and `docker push`. I use GitHub Actions for this, because it has a lot of built-in functionality to do this easy and fast. See [this workflow file](https://github.com/boostorg/mysql/blob/develop/.github/workflows/docker-linux.yml). I'm pushing this to an `anarthal-containers` account - that's legacy and I want to get rid of it. I think you can push them to `ghcr.io/boostorg/redis` - but I'm not sure about this point. The main thing is that the images are public so your CIs can pick them.  
* Finally, use this images in your CIs. Most CIs support something called "services" - this is, running an instance of something like a database in the background for the duration of your build. For instance, see [this workflow file](https://github.com/boostorg/mysql/blob/develop/.github/workflows/coverage.yml). Things to note here:  
    * The `services.<service-name>.image` property should point to the image you built. This will run your Redis server with the configuration you want, for all builds.  
    * The `services.<service-name>.ports` should include a port binding for the ports you're using - likely `6379:6379` in your case.  
    * If you're using GitHub actions, your server will become available under the hostname of `<service-name>`. In my case, I use `"mysql"`, so that's the hostname I use in my tests.  
    * You don't need the volume mount I have, that's for UNIX sockets, which you don't need.  
    * In the case of GHA, this is only available for Linux. I don't know about Drone (I need a localhost MySQL in Windows for named pipes). You probably can't make it work on OSX at all. You can split the tests requiring SSL to a separate build job that's only run on Linux, and go.  
  
Should you have any questions or need any help with this, please let me know.

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-03-20 22:23:55 UTC  
> Url: https://github.com/boostorg/redis/issues/191#issuecomment-2010753548  

@anarthal Thanks for detailed overview. Would it make sense to enhance the container you are using with a Redis server so I can avoid all the trouble?

---

## Comment 3

> Username: anarthal  
> Created at: 2024-03-21 07:27:57 UTC  
> Url: https://github.com/boostorg/redis/issues/191#issuecomment-2011373583  

I'm afraid it does not. The MySQL containers are just MySQL. I'm also using build containers (which I don't think you're using), but these don't contain MySQL at all. It's good practice to keep containers as small as possible.  
  
I can write you a minimal Dockerfile, GHA workflow to build an image, and GHA workflow to run your CI with a local Redis server, but you will need to adjust it afterwards to run the tests only for that workflow. I'll also need the specific configuration you need for your tests.

---

## Comment 4

> Username: mzimbres  
> Created at: 2024-03-27 19:43:27 UTC  
> Url: https://github.com/boostorg/redis/issues/191#issuecomment-2023831782  

> I can write you a minimal Dockerfile, GHA workflow to build an image, and GHA workflow to run your CI with a local Redis server, but you will need to adjust it afterwards to run the tests only for that workflow. I'll also need the specific configuration you need for your tests.  
  
Any help would be much appreciated, thanks.

---

## Comment 5

> Username: anarthal  
> Created at: 2024-03-27 20:44:46 UTC  
> Url: https://github.com/boostorg/redis/issues/191#issuecomment-2023952998  

Does the server need to have any particular configuration regarding SSL?

---

## Comment 6

> Username: mzimbres  
> Created at: 2024-03-30 20:29:17 UTC  
> Url: https://github.com/boostorg/redis/issues/191#issuecomment-2028462232  

The TLS tests are very simple and won't even validate server the certificate. At the moment I am using the following parameters  
  
```cpp  
config cfg;  
cfg.use_ssl = true;  
cfg.username = "aedis";  
cfg.password = "aedis";  
cfg.addr.host = "db.occase.de";  
cfg.addr.port = "6380";  
```  
  
but those are rather arbitrary.  
  
My server on `db.occase.de` was also exposed to the network so I set an ACL that would only accept `PING` commands from the `aedis` user. Thefore I also used the TLS server to [test](https://github.com/boostorg/redis/blob/5c46b62958168bd746fd7560041498e105f70b32/test/test_conn_tls.cpp#L95) how the handshake works in this case. You can ignore this at first because ACL can be set on the client side via commands too, no need to put on the config files.
