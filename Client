//Made by IGAZIN Daulet
#include <iostream>
#include <ws2tcpip.h>
#include <string>
#include <fstream>
#include <vector>
#pragma comment(lib, "ws2_32.lib")

using namespace std;

vector<string> split(string userInput) {
    //function that uses vector of strings to store separate words from userInput
    vector<string> res;
    int length = userInput.length();
    string word = "";
    int count = 0;

    for (auto x : userInput) {
        if (x == ' ' || x == '\n') {
            res.push_back(word);
            word = "";
            count++;
            if (count == 2) {
                break;
            }
        }
        else {
            word += x;
        }
    }

    //push back last word and in case if the size is 1 add empty string to 
    res.push_back(word);
    if (res.size() == 1) {
        res.push_back("");
    }

    return res;
}

void main() {
    string ipAddress; // IP adress of the server
    cout << "Input IP adress: ";
    getline(cin, ipAddress); //"127.0.0.1" for local host
    int port = 16000;        // Listening port # on the server

    // Initialize Winsock
    WSAData data;
    WORD ver = MAKEWORD(2, 2);
    int wsResult = WSAStartup(ver, &data);
    if (wsResult != 0) {
        cerr << "Can't start Winsock, Err #" << wsResult << endl;
        return;
    }

    // Create Socket
    SOCKET sock = socket(AF_INET, SOCK_STREAM, 0);
    if (sock == INVALID_SOCKET) {
        cerr << "Can't create socket, Err #" << WSAGetLastError() << endl;
        WSACleanup();
        return;
    }

    while (true) {      //takes time to ask again for valid ip address
        // Fill in a hint structure
        sockaddr_in hint;
        hint.sin_family = AF_INET;
        hint.sin_port = htons(port);
        inet_pton(AF_INET, ipAddress.c_str(), &hint.sin_addr);

        // Connect to server in case of wrong ip adress ask to rewrite it
        int connResult = connect(sock, (sockaddr*)&hint, sizeof(hint));
        if (connResult == SOCKET_ERROR) {
            cerr << "Can't connect to server, Err #" << WSAGetLastError() << endl;
            cout << "Please, input valid IP adress: ";
            getline(cin, ipAddress);
        }
        else break;
    }

    // Do-while loop to send and receive data, loop is terminated only when "exit" is written
    char buf[4096];
    string userInput;
    do {
        //input
        cout << ">>> ";
        getline(cin, userInput);

        if (userInput.size() > 0) {
            //split function which splits userInput into string elements
            vector<string> input = split(userInput);
            string command = input[0];

            if (command == "UPLOAD") {
                string filename = input[1];
                string response;

                if (filename.length() == 0) {
                    //send userInput to server
                    send(sock, userInput.c_str(), userInput.size(), 0);

                    //send to server
                    ZeroMemory(buf, 4096);
                    int bytesReceived = recv(sock, buf, 4096, 0);

                    //echo to console
                    cout << string(buf, 0, bytesReceived) << endl;
                    continue;
                }
                ifstream infile(filename);

                if (infile.is_open()) {
                    //send userInput to server
                    send(sock, userInput.c_str(), userInput.size(), 0);

                    //send to server
                    ZeroMemory(buf, 4096);
                    int bytesReceived = recv(sock, buf, 4096, 0);

                    //echo to console
                    response = string(buf, 0, bytesReceived);
                    cout << response << endl;

                    if (response == "SUCCESS") {
                        //create firstline which include ip adress, port and file name
                        string firstline = ipAddress + " " + to_string(port) + " " + filename.substr(0, filename.length() - 4) + "\n";

                        //send to server
                        send(sock, firstline.c_str(), firstline.size(), 0);
                        ZeroMemory(buf, 4096);

                        //print acknowledgement of first line
                        int receive = recv(sock, buf, 4096, 0);
                        cout << string(buf, 0, receive) << " for line: " << firstline.substr(0, firstline.length() - 1) << endl;

                        //string in which each line will be put and then sent to server
                        string line;
                        while (getline(infile, line)) {
                            // send line to server
                            line.push_back('\n');
                            send(sock, line.c_str(), line.size(), 0);

                            // receive ack messages and print them
                            ZeroMemory(buf, 4096);
                            receive = recv(sock, buf, 4096, 0);
                            cout << string(buf, 0, receive) << " for line: " << line.substr(0, line.length() - 1) << endl;
                            if (line == "#\n") {
                                cout << string(buf, 0, receive) << " for EOF" << endl;
                            }
                        }
                    }
                }
                else cout << "Can not open file" << endl;
                infile.close();
            }

            else if (command == "DOWNLOAD") {
                //send userInput to server
                send(sock, userInput.c_str(), userInput.size(), 0);

                string filename = input[1];
                string response;

                // receive message from server (either "SUCCESS" or "ERROR")
                ZeroMemory(buf, 4096);
                int bytesReceived = recv(sock, buf, 4096, 0);
                response = string(buf, 0, bytesReceived);
                cout << response << endl;

                //string which will include messages sent by server
                string line = "";

                if (response == "SUCCESS") {
                    //create a file which adds "_download.txt" at the end of the file name
                    ofstream myfile;
                    string downloadfile = filename.substr(0, filename.length() - 4) + "_download.txt";
                    myfile.open(downloadfile);

                    if (myfile.is_open()) {
                        while (true) {       // can be changed to while(true) since break statement is written in code
                            //receive message (line) from server
                            ZeroMemory(buf, 4096);
                            int bytesReceived = recv(sock, buf, 4096, 0);

                            //put the message into string line
                            line = string(buf, 0, bytesReceived);
                            if (line == "#") {
                                cout << "EOF received: " << line << endl;
                                cout << "Saving to " << downloadfile << endl;
                                break;
                            }

                            //write lines into file
                            myfile << line;

                            //print which line is received and send acknowledgement to server
                            cout << "Line received: " << line.substr(0, line.length() - 1) << endl;
                            send(sock, "ACK", 3, 0);
                        }
                        if (line == "#") {
                            send(sock, "ACK", 3, 0);
                        }
                    }
                    else cout << "Cannot open file" << endl;
                    myfile.close();
                }
            }
            else if (command == "exit") {
                //send userInput to server
                send(sock, userInput.c_str(), userInput.size(), 0);

                //send "exit" to server to close TCP connection and main
                closesocket(sock);
                WSACleanup();
                return;
            }
            else {
                //send userInput to server
                send(sock, userInput.c_str(), userInput.size(), 0);

                //print the message applicable to RETRIEVE too since it only prints message from server
                ZeroMemory(buf, 4096);
                int bytesReceived = recv(sock, buf, 4096, 0);
                cout << string(buf, 0, bytesReceived) << endl;
            }
        }
    } while (true);
}
