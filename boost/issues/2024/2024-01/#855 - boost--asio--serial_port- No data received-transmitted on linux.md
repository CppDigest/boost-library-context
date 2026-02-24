# #855 - boost::asio::serial_port: No data received/transmitted on linux [Open]

> Username: Gibson85  
> Created at: 2024-01-24 15:04:04 UTC  
> Updated at: 2024-01-24 15:04:04 UTC  
> Url: https://github.com/boostorg/boost/issues/855  

Hi, I am using the boost::asio::serial_port on windows without probplems. Now I have still one maschine running on windows and one on linux. Using a serial port connection via putty works to send and transmit data between the two maschines. Now I start a serial_port on the linux system and nothing works but also no errors are thrown. Basically I do a...  
  
socket.open("/dev/ttyAMA1", ec);  
socket.set_option(serial_port_base::baudrate(9600), ec);  
socket.set_option(serial_port_base::character_size(8), ec);  
socket.set_option(serial_port_base::parity(serial_port_base::parity::none), ec);  
socket.set_option(serial_port_base::stop_bits(serial_port_base::stop_bits::one), ec);  
socket.set_option(serial_port_base::flow_control(serial_port_base::flow_control::none), ec);  
  
The error code variable ec never indicates an error.  
If I use e.g. an not existing device, I get an error as expected.  
  
For receiving/transmitting I use...  
  
socket.async_read_some(mutable_buffer(...), std::bind(...));  
async_write(socket, const_buffer(...), std::bind(...));  
  
...functions. Am I missing something?
