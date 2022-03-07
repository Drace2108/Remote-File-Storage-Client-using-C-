# Remote-File-Storage-Client-using-C-
# Instructions

Since I used #pragma comment(lib, "ws2_32.lib") in this code (which gcc ignores on windows), open the [program].cpp in Visual Studio 2019 and compile it within it, subsequently opening command line and proceeding functions

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
