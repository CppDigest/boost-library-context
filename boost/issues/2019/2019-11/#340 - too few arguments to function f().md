# #340 - too few arguments to function f() [Closed]

> Username: hosseinpg  
> Created at: 2019-11-03 09:15:01 UTC  
> Updated at: 2019-11-06 12:22:33 UTC  
> Closed at: 2019-11-06 12:22:33 UTC  
> Url: https://github.com/boostorg/boost/issues/340  

Dear all,  
I'm using boost along with PCL and Intel libRealsense libraries. I've installed the dev version of the boost. While trying to run a PCL visualize in a different thread I get following error(my code also can be seen after the output). Any help will be very appreciated:  
  
[ 33%] Building CXX object CMakeFiles/scanner.dir/src/scanner.cpp.o  
In file included from /usr/include/boost/thread/thread_only.hpp:22:0,  
                 from /usr/include/boost/thread/thread.hpp:12,  
                 from /usr/include/boost/thread.hpp:13,  
                 from /usr/local/include/pcl-1.9/pcl/io/boost.h:51,  
                 from /usr/local/include/pcl-1.9/pcl/io/file_io.h:43,  
                 from /usr/local/include/pcl-1.9/pcl/io/pcd_io.h:44,  
                 from /home/hossein/software/3dscanner/footscanner/src/scanner.cpp:9:  
/usr/include/boost/thread/detail/thread.hpp: In instantiation of ‘void boost::detail::thread_data<F>::run() [with F = void (*)(boost::shared_ptr<const pcl::PointCloud<pcl::PointXYZ> >)]’:  
/home/hossein/software/3dscanner/footscanner/src/scanner.cpp:501:41:   required from here  
/usr/include/boost/thread/detail/thread.hpp:116:17: error: too few arguments to function  
                 f();  
  
My code:   
  
 ...  
bool update;  
boost::mutex updateModelMutex;  
pcl::PointCloud<pcl::PointXYZRGB>::Ptr cloud (new pcl::PointCloud<pcl::PointXYZRGB>);  
  
void visualize()    
  
int main()  
    {  
        //Start visualizer thread  
        boost::thread workerThread(visualize);   
  
        while(notFinishedProcessing)  
        {  
           boost::mutex::scoped_lock updateLock(updateModelMutex);  
          update = true;  
          // do processing on cloud  
           updateLock.unlock();  
  
        }  
        workerThread.join();    
    }  
      
void visualize()    
    {    
        // prepare visualizer named "viewer"  
  
        while (!viewer->wasStopped ())  
        {  
            viewer->spinOnce (100);  
            // Get lock on the boolean update and check if cloud was updated  
            boost::mutex::scoped_lock updateLock(updateModelMutex);  
            if(update)  
            {  
                if(!viewer->updatePointCloud(cloud, "sample cloud"))  
                  viewer->addPointCloud(cloud, colorHandler, "sample cloud");  
                update = false;  
            }  
            updateLock.unlock();  
  
        }     
   }  
...
