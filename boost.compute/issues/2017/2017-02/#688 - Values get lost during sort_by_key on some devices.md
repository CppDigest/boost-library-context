# #688 - Values get lost during sort_by_key on some devices [Closed]

> Username: wierich  
> Created at: 2017-02-01 13:14:40 UTC  
> Updated at: 2017-04-09 18:02:55 UTC  
> Closed at: 2017-04-09 18:02:55 UTC  
> Url: https://github.com/boostorg/compute/issues/688  

While the keys are sorted correctly on all devices, some values get lost during the sort_by_key algorithm. If you accumulate all values of the vector, the result of the values vector is different after sorting on the following devices:  
1. Intel(R) HD Graphics 5500  
2. Intel(R) HD Graphics 530  
3. NVIDIA Quadro K620M  
4. NVIDIA Geforce GTX 970  
5. NVIDIA Quadro K4000  
  
Operating system Windows 7  
Compiler VS 2012  
  
On Intel CPUs and Sapphire Nitro Radeon R9 380X the example works properly.  
  
```cpp  
#include <boost/compute/algorithm/sort_by_key.hpp>  
  
const int NUMBER_OF_DIFFERENT_VALUES_SSBK = 500;  
const int NUMBER_OF_REPETITIONS_SSBK = 900;  
  
BOOST_COMPUTE_FUNCTION(bool, Less_SSBK, (int lhs, int rhs),  
{  
	return lhs < rhs;  
});  
  
int main()  
{  
	// show devices  
	int i=0;  
	for (const auto &device : boost::compute::system::devices())  
	{  
		std::cout << "Device number: " << i << std::endl;  
		std::cout << "  Device name: " << device.name() << std::endl;  
		std::cout << "  OpenCL Version: " << device.version() << std::endl;  
		std::cout << "  Vendor: " << device.vendor() << std::endl;  
		std::cout << "  Global Memory Size: " << device.global_memory_size() << std::endl;  
		i++;  
	}  
  
	// select device  
	boost::compute::device device;  
	std::vector<boost::compute::device> devices = boost::compute::system::devices();  
	std::cout << "Device number: ";  
	std::string selection;  
	std::cin >> selection;  
	device = devices[std::stoi(selection)];  
	boost::compute::context context = boost::compute::context(device);  
	boost::compute::command_queue queue = boost::compute::command_queue(context, device);  
	std::cout << device.name() << " -- " << device.version() << std::endl;  
  
	std::cout << "Show input data (0 = false, 1 = true): ";  
	std::cin >> selection;  
	bool show_input_data = std::stoi(selection) == 1;  
  
	std::cout << "Prepare data..." << std::endl;  
  
	std::vector<int> different_key_vector(NUMBER_OF_DIFFERENT_VALUES_SSBK);  
	std::vector<int> different_val_vector(NUMBER_OF_DIFFERENT_VALUES_SSBK);  
  
	for (short i = 0; i < NUMBER_OF_DIFFERENT_VALUES_SSBK; i++)  
	{  
		different_key_vector[i] = i;  
		different_val_vector[i] = i;  
	}  
  
	std::vector<int> host_key_vector;  
	std::vector<int> host_val_vector;  
	host_key_vector.reserve(NUMBER_OF_DIFFERENT_VALUES_SSBK * NUMBER_OF_REPETITIONS_SSBK);  
	host_val_vector.reserve(NUMBER_OF_DIFFERENT_VALUES_SSBK * NUMBER_OF_REPETITIONS_SSBK);  
  
	for (int i = 0; i < NUMBER_OF_REPETITIONS_SSBK; i++)  
	{  
		for (int j = 0; j < NUMBER_OF_DIFFERENT_VALUES_SSBK; j++)  
		{  
			int k = rand() % (NUMBER_OF_DIFFERENT_VALUES_SSBK);  
			host_key_vector.push_back(different_key_vector[k]);  
			host_val_vector.push_back(different_val_vector[k]);  
		}  
	}  
  
	// print out input vector  
	int wrong_data = 0;  
	int counted_data = 0;  
	std::cout << "input:  [ ";  
	for(size_t i = 0; i < host_key_vector.size(); i++){  
		if (host_key_vector[i] == host_val_vector[i])  
		{  
			if (show_input_data)  
			{  
				std::cout << host_key_vector[i] << " : " << host_val_vector[i];  
			}  
		}  
		else  
		{  
			if (show_input_data)  
			{  
				std::cout << "\n!!!" << host_key_vector[i] << " : " << host_val_vector[i] << "!!!\n";  
			}  
			wrong_data++;  
		}  
  
		if(show_input_data && i != host_key_vector.size() - 1){  
			std::cout << ", ";  
		}  
  
		counted_data++;  
	}  
	std::cout << " ]" << std::endl;  
	std::cout << "wrong_data: " << wrong_data << std::endl;  
	std::cout << "size: " << host_key_vector.size() << " " << host_val_vector.size() <<  
		" calculated: " << NUMBER_OF_DIFFERENT_VALUES_SSBK * NUMBER_OF_REPETITIONS_SSBK <<  
		" counted: " << counted_data << std::endl;  
	std::cout << "sum keys: " << std::accumulate(host_key_vector.begin(), host_key_vector.end(), 0) <<  
		", sum values: " << std::accumulate(host_val_vector.begin(), host_val_vector.end(), 0) << std::endl;  
  
	// transfer the values to the device  
	boost::compute::vector<int> device_key_vector(host_key_vector, queue);  
	boost::compute::vector<int> device_val_vector(host_val_vector, queue);  
  
	// sort the values on the device by key  
	std::cout << "Sort by key..." << std::endl;  
	boost::compute::sort_by_key(device_key_vector.begin(), device_key_vector.end(), device_val_vector.begin(), Less_SSBK, queue);  
  
	// check sorted values  
	host_key_vector.resize(device_key_vector.size());  
	host_val_vector.resize(device_val_vector.size());  
  
	// transfer the values back to the host  
	boost::compute::copy(device_key_vector.begin(),  
		device_key_vector.end(),  
		host_key_vector.begin(),  
		queue);  
  
	boost::compute::copy(device_val_vector.begin(),  
		device_val_vector.end(),  
		host_val_vector.begin(),  
		queue);  
	// check sorted data and print out failures  
	std::cout << "Analyse sorted data..." << std::endl;  
	wrong_data = 0;  
	int wrong_sort = 0;  
	int counted_items = 0;  
	for(size_t i = 0; i < host_key_vector.size(); i++){  
		if (host_val_vector[i] == host_key_vector[i])  
		{  
			if ((i == host_key_vector.size() - 1) || (host_key_vector[i] == host_key_vector[i+1] - 1) || (host_key_vector[i] == host_key_vector[i+1]))  
			{  
			}  
			else  
			{  
				std::cout << "***" << host_key_vector[i] << " : " << host_val_vector[i] << "***\n";  
				wrong_sort++;  
			}  
		}  
		else  
		{  
			std::cout << "!!!" << host_key_vector[i] << " : " << host_val_vector[i] << "!!!\n";  
			wrong_data++;  
		}  
		counted_items++;  
	}  
	std::cout << "wrong_data: " << wrong_data << std::endl;  
	std::cout << "wrong_sort: " << wrong_sort << std::endl;  
	std::cout << "size: " << host_key_vector.size() << " " << host_val_vector.size() <<  
		" calculated: " << NUMBER_OF_DIFFERENT_VALUES_SSBK * NUMBER_OF_REPETITIONS_SSBK <<  
		" counted: " << counted_items  << std::endl;  
	std::cout << "sum keys: " << std::accumulate(host_key_vector.begin(), host_key_vector.end(), 0) <<  
		", sum values: " << std::accumulate(host_val_vector.begin(), host_val_vector.end(), 0) << std::endl;  
  
	return 0;  
}  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-04-05 20:54:05 UTC  
> Url: https://github.com/boostorg/compute/issues/688#issuecomment-291993651  

I've found the bug. I'll post and merge the fix tomorrow.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-04-09 18:02:55 UTC  
> Url: https://github.com/boostorg/compute/issues/688#issuecomment-292801651  

Fixed by https://github.com/boostorg/compute/pull/702.
