# Remote-File-Storage-Client-using-C-
# Instructions

## Server

The server file *server.py* is written in Python 3 (suggested version >= 3.5.0). You can run the server by the following command:

    python server.py

or

    python3 server.py

if both Python 2.X and Python 3.X were installed in your system. You could download Python from its [official website](https://www.python.org/). Below are the installation guide for Python 3.X for Windows/MacOS/Ubuntu.

Windows: https://docs.python.org/3/using/windows.html

MacOS: https://docs.python.org/3/using/mac.html

Ubuntu: https://docs.python.org/3/using/unix.html


*Notes: Python 2 and 3 are usually pre-installed in Ubuntu system. You could always try the command "python" or "python3" in terminal to check it out*

Special Notes for the server: 

1. Generally speaking, TCP packets sent by the server should only contain one particular word or one line, e.g. "SUCCESS" or "ERROR: the file does not exist" or "xxxxxxxxxxxxxxxxx" (the first line of your downloading file). However, you might encounter a case where the packet sent by server contains the content of two or more packets, e.g. "SUCCESSxxxxxxxxxxxxxxxxx". This is because the [Nagle's algorithm](https://en.wikipedia.org/wiki/Nagle%27s_algorithm) used by Python's TCP protocol automatically combines small TCP packets that were sent in a short time. To avoid such cases, a simple solution is to let the server sleep for some time after sending each packet. That's why there is a time.sleep(SENDING_COOLDOWN) command after each send() function in the server's source code. The default SENDING_COOLDOWN value is set to 0.1 second. This value is enough when both server and client are running on the same computer. But when they were executed on two computers, letting the server sleep for 0.1 second might not be enough to solve the problem (this depends on the delay between the two computers). If you encounter such cases, you could change the default value of SENDING_COOLDOWN to a larger value, e.g. 0.5 second or 1 second. SENDING_COOLDOWN is defined at line 6 of server.py.

2. If your client accidentally quits during file transmission ("UPLOAD" or "DOWNLOAD"), the server would be stuck waiting for next line in the file or ACK frame. In such case, the server should be restarted.

## Client

Since I used #pragma comment(lib, "ws2_32.lib") in this code (which gcc ignores on windows), open the [program].cpp in Visual Studio 2019 (download from https://visualstudio.microsoft.com/) and compile it within it, subsequently opening command line and proceeding functions

The client side program will first show:
"Input IP adress: "

Input the server IP, and the server will tell the client side whether it's connected.

When server can't start Winsock, it will show:
"Can't start Winsock, Err #errorcode"

When server can't create a socket, it will show:
"Can't create socket, Err #errorcode"

When server IP is invalid, it will show:
"Can't connect to server, Err #errorcode
Please, input valid IP adress: "

If it's connected, it will show:
">>>", client can start to input some instructions.

When the client wants to upload a file, type "UPLOAD "+filename, the server will send a response. If the upload is successfull, the response will be:
"SUCCESS"
ACK for line: " + server IP adress + port number + filename
"ACK for line: " + first line of the file
...
"ACK for line: #"(last line of the file)
"ACK for line: EOF"
If the upload cannot find file, it will show:
"Cannot open file"

When the client wants to download a file, type "DOWNLOAD "+filename, the server will send a response. If the download is successfull, the response will be:
"SUCCESS"
Line received: " + server IP adress + port number + filename
"Line received: " + first line of the file
...
"Line received: #"(last line of the file)
"Line received: EOF
Saving to hello_download.txt"
If the file creation is failed, it will show:
"Cannot open file"

When the client wants to retrieve a file, type "RETRIEVE "+filename, the server will send a response. The response will either be "Yes" if the file exists and "No" if it does not.

When the client wants to exit, type:
"exit", the server will close the TCP connection.
