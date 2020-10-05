# thrift-tutorial

In this tutorial, we will walk through the Apacha Thrift tutorial code creating a simple remote calculator service. The client sends an RPC call to the server, giving details of the operation to be performed, and the server returns the result.

The tutorial code is taken from the ```tutorial``` folder in Apache Thrift release 0.13.0. Modifications are made to use the correct paths on remote.cs.binghamton.edu environment. We also included wrapper scripts, server.sh and client.sh, to set proper environment variables (in case of C++) and set dependent jar library classpath (in case of Java) before running the program. 


## Setting up the Environment

1. Open the terminal/login to your remote machine and make sure you're in bash shell:
```
cs557-inst@remote07:~$ bash
cs557-inst@remote07:~$ echo $0
bash
cs557-inst@remote07:~$
```

2. Add the thrift compiler path to your PATH environment variable:
```
export PATH=$PATH:/home/cs557-inst/local/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:"/home/cs557-inst/local/lib"
```
OR

  Add these two lines to your .bashrc file and `source ~/.bashrc`.

3. If you set the environment correctly, you should get the following output:
```
cs557-inst@remote07:~/thrift-tutorial$ which thrift
/home/yaoliu/src_code/local/bin/thrift
cs557-inst@remote07:~/thrift-tutorial$ thrift --version
Thrift version 0.13.0
cs557-inst@remote07:~/thrift-tutorial$
```

4. Clone this repo and cd into the repo.
```
cs557-inst@remote07:~$ git clone https://github.com/BingU-CS457-CS557/thrift-tutorial
...
cs557-inst@remote07:~$ cd thrift-tutorial/
cs557-inst@remote07:~/thrift-tutorial$
```


## Generating stub code, compilation and execution

Inside thrift-tutorial directory, you'll see two files:
* tutorial.thrift : Contains the thrift definitions of interfaces, services and custom datatypes/structs for our application. This is the Interface Definition File. Very well documented. Please read this file.
* shared.thrift : Another thrift file which defines shared/common structs and services. This file is imported by tutorial.thrift.

Now, there are 3 folders for the languages that you're going to use. Go to the folder of your language and generate the client stub and server stub code by running the following command:

* For CPP:
```
cd cpp/
thrift -r --gen cpp ../tutorial.thrift
```

* For Java:
```
cd java/
thrift -r --gen java ../tutorial.thrift
```

* For Python:
```
cd py/
thrift -r --gen py ../tutorial.thrift
```

Note that the tutorial.thrift file is outside of individual language directories, hence, we're doing `"../tutorial.thrift"`

Now, this will generate a folder called "gen-\<language\>" in your current directory (e.g. `gen-cpp`, `gen-java`, `gen-py`)
This folder contains the generated client-stub code, server-stub code and (maybe) a common header file.

Now we'll compile and run the client and servers.

*NOTE: If you're using C++ or Java, you should know how to compile/build your program using "Makefile" and "make" commands. This is because in CS457/557, you're expected to provide a Makefile with all your C++/Java assignments which will compile your code and generate the executables.*

___
### Python
1. Make sure the following two lines are at the top of your source code for BOTH client and server:
```
sys.path.append('gen-py')
sys.path.insert(0, glob.glob('/home/cs557-inst/local/lib/py/build/lib*')[0])
```
A little careful with the first line. It requires that your "gen-py" folder should be in the SAME directory as where your program is run from. (We'll run our program with client.sh and server.sh which are in same directory)

2. Also, make sure that all the thrift related imports should be BELOW the second line, since this line adds the path from which all the python thrift libraries come from.

3. Start the server and pass a port number as argument (please use a different port number):
```
cs557-inst@remote00:~/thrift-tutorial/py$ ./server.sh 9090
Starting the server...
```

4. Run the client and pass to it the server IP address and port number:
```
cs557-inst@remote07:~/thrift-tutorial/py$ ./client.sh 128.226.114.200 9090
ping()
1+1=2
InvalidOperation: InvalidOperation(whatOp=4, why=u'Cannot divide by 0')
15-10=5
Check log: 5
cs557-inst@remote07:~/thrift-tutorial/py$
```

___
### Java
1. Generate the classes by typing "make" command. Makefile is already present in the repo.
```
cs557-inst@remote07:~/thrift-tutorial/java$ make
rm -rf bin/
mkdir bin
mkdir bin/client_classes
mkdir bin/server_classes
javac -classpath /home/cs557-inst/local/lib/libthrift-0.13.0.jar:/home/cs557-inst/local/lib/slf4j-api-1.7.30.jar:/home/cs557-inst/loca/lib/slf4j-log4j12-1.7.12.jar:/home/cs557-inst/local/lib/javax.annotation-api-1.3.2.jar -d bin/client_classes/ src/JavaClient.java src/CalculatorHandler.java gen-java/tutorial/* gen-java/shared/*
javac -classpath /home/cs557-inst/local/lib/libthrift-0.13.0.jar:/home/cs557-inst/local/lib/slf4j-api-1.7.30.jar:/home/cs557-inst/loca/lib/slf4j-log4j12-1.7.12.jar:/home/cs557-inst/local/lib/javax.annotation-api-1.3.2.jar -d bin/server_classes/ src/JavaServer.java src/CalculatorHandler.java gen-java/tutorial/* gen-java/shared/*
Note: src/JavaServer.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
cs557-inst@remote07:~/thrift-tutorial/java$
```

2. Run the server by providing it the port number to listen to (please use a different port number):
```
cs557-inst@remote07:~/thrift-tutorial/java$ ./server.sh 9090
Starting the simple server...
```

3. Run the client by providing it the IP and port number to connect to:
```
cs557-inst@remote00:~/thrift-tutorial/java$ ./client.sh 128.226.180.169 9090
Received 1
ping()
Received 2
1+1=2
Received 3
Invalid operation: Cannot divide by 0
Received 4
15-10=5
Received 5
Check log: 5
cs557-inst@remote00:~/thrift-tutorial/java$
```

___
### C++
1. First, make sure that the path `/home/yaoliu/src_code/local/lib` is added to your `$LD_LIBRARY_PATH` environment variable.
```
cs557-inst@remote07:~/thrift-tutorial$ echo $LD_LIBRARY_PATH
:/home/cs557-inst/local/lib
cs557-inst@remote07:~/thrift-tutorial$
```

2. Then, generate the binaries by typing the "make" command. Makefile is already present in the repo
```
cs557-inst@remote07:~/thrift-tutorial/cpp$ make
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c src/CppServer.cpp -o CppServer.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c gen-cpp/Calculator.cpp -o Calculator.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c gen-cpp/SharedService.cpp -o SharedService.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c gen-cpp/tutorial_constants.cpp -o tutorial_constants.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c gen-cpp/tutorial_types.cpp -o tutorial_types.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c gen-cpp/shared_constants.cpp -o shared_constants.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c gen-cpp/shared_types.cpp -o shared_types.o
g++ CppServer.o Calculator.o SharedService.o tutorial_constants.o tutorial_types.o shared_constants.o shared_types.o -o server -std=c++11 -lstdc++ -L/home/cs557-inst/local/lib/ -lthrift 
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/cs557-inst/local/include -Isrc/ -I/home/cs557-inst/local/include/thrift -c src/CppClient.cpp -o CppClient.o
g++ CppClient.o Calculator.o SharedService.o tutorial_constants.o tutorial_types.o shared_constants.o shared_types.o -o client  -std=c++11 -lstdc++ -L/home/cs557-inst/local/lib/ -lthrift 
cs557-inst@remote07:~/thrift-tutorial/cpp$
```

3. Run the server and pass it a port number to listen to (please use a different port number):
```
cs557-inst@remote07:~/thrift-tutorial/cpp$ ./server.sh 9090
Starting the server...
```

5. Run the client and pass it the server IP address and port number:
```
cs557-inst@remote00:~/thrift-tutorial/cpp$ ./client.sh 128.226.114.207 9090
ping()
1 + 1 = 2
InvalidOperation: Cannot divide by 0
15 - 10 = 5
Received log: SharedStruct(key=1, value=5)
cs557-inst@remote00:~/thrift-tutorial/cpp$
```
___

## Description:

The server implements Calculator Service defined in tutorial.thrift. This service contains functions like ping(), add(), calculate() and zip(). The add() function simply takes two integers and returns their sum. The calculate() function takes an integer called "logid", and a struct called "work". The "work" struct contains an enum 'Operation', which indicates what operation is to be performed on two integers present in the same struct as 'num1' and 'num2'. If the operation divide by zero, the custom exception "InvalidOperation" is raised. Otherwise, the result of the operation returned back to the client.

The client, after opening a connection, first calls the ping() function. Then it calls the add() function with 1+1. Then to invoke the InvalidOperation exception, it attempts a division by zero. Then client performs a subtraction and checks logs. 

___

*NOTE: We have small wrapper scripts, server.sh and client.sh to run the servers and clients. These scripts allow us to set the proper environment variables (in case of C++) and set the dependent jar library classpath (in case of Java) before running the program. You'll be expected to provide similar wrapper scripts for programming assignments.* 

