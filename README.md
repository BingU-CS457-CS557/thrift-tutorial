# thrift-lab

## Setting up the Environment

1. Open the terminal/login to your remote machine and make sure you're in bash shell:
```
vchaska1@remote07:~$ bash
vchaska1@remote07:~$ echo $0
bash
vchaska1@remote07:~$
```

2. Add the thrift compiler path to your PATH environment variable:
```
export PATH=$PATH:/home/yaoliu/src_code/local/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:"/home/yaoliu/src_code/local/lib"
```
OR

  Add these two lines to your .bashrc file and `source ~/.bashrc`.

3. If you set the environment correctly, you should get the following output:
```
vchaska1@remote07:~/thrift-lab$ which thrift
/home/yaoliu/src_code/local/bin/thrift
vchaska1@remote07:~/thrift-lab$ thrift --version
Thrift version 0.10.0
vchaska1@remote07:~/thrift-lab$
```

4. Clone this repo (https://github.com/vipulchaskar/thrift-lab) and cd into the repo.
```
vchaska1@remote07:~$ git clone https://github.com/vipulchaskar/thrift-lab
...
vchaska1@remote07:~$ cd thrift-lab/
vchaska1@remote07:~/thrift-lab$
```

**TODO: Explain what our application will do.**


## Generating stub code and compilation

Inside thrift-lab directory, you'll see two files:
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

### Python
1. Add the following two lines at the top of your source code for BOTH client and server:
```
sys.path.append('gen-py')
sys.path.insert(0, glob.glob('/home/yaoliu/src_code/local/lib/lib/python2.7/site-packages')[0])
```
A little careful with the first line. It requires that your "gen-py" folder should be in the SAME directory as where your program is run from. (We'll run our program with client.sh and server.sh which are in same directory)

2. Also, make sure that all the thrift related imports should be BELOW the second line, since this line adds the path from which all the python thrift libraries come from.

3. Start the server:
```
vchaska1@remote00:~/thrift-lab/py$ ./server.sh
Starting the server...
```

4. Make sure that you have entered correct server IP and port in Client code - PythonClient.py. Then, run the client:
vchaska1@remote07:~/thrift-lab/py$ ./client.sh
```
ping()
1+1=2
InvalidOperation: InvalidOperation(whatOp=4, why=u'Cannot divide by 0')
15-10=5
Check log: 5
vchaska1@remote07:~/thrift-lab/py$
```

___
### Java
1. Generate the classes by typing "make" command. Makefile is already present in the repo.
```
vchaska1@remote07:~/thrift-lab/java$ make
rm -rf bin/
mkdir bin
mkdir bin/client_classes
mkdir bin/server_classes
javac -classpath /home/yaoliu/src_code/local/lib/usr/local/lib/libthrift-0.10.0.jar:/home/yaoliu/src_code/local/lib/usr/local/lib/slf4j-log4j12-1.7.12.jar:/home/yaoliu/src_code/local/lib/usr/local/lib/slf4j-api-1.7.12.jar -d bin/client_classes/ src/JavaClient.java src/CalculatorHandler.java gen-java/tutorial/* gen-java/shared/*
javac -classpath /home/yaoliu/src_code/local/lib/usr/local/lib/libthrift-0.10.0.jar:/home/yaoliu/src_code/local/lib/usr/local/lib/slf4j-log4j12-1.7.12.jar:/home/yaoliu/src_code/local/lib/usr/local/lib/slf4j-api-1.7.12.jar -d bin/server_classes/ src/JavaServer.java src/CalculatorHandler.java gen-java/tutorial/* gen-java/shared/*
Note: src/JavaServer.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
vchaska1@remote07:~/thrift-lab/java$
```

2. Run the server by providing it the port number to listen to:
```
vchaska1@remote07:~/thrift-lab/java$ ./server.sh 9090
Starting the simple server...
```

3. Run the client by providing it the IP and port number to connect to:
```
vchaska1@remote00:~/thrift-lab/java$ ./client.sh 128.226.180.169 9090
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
vchaska1@remote00:~/thrift-lab/java$
```

___
### C++
1. First, make sure that the path `/home/yaoliu/src_code/local/lib` is added to your `$LD_LIBRARY_PATH` environment variable.
```
vchaska1@remote07:~/thrift-lab$ echo $LD_LIBRARY_PATH
:/home/yaoliu/src_code/local/lib
vchaska1@remote07:~/thrift-lab$
```

2. Then, generate the binaries by typing the "make" command. Makefile is already present in the repo
```
vchaska1@remote07:~/thrift-lab/cpp$ make
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c src/CppServer.cpp -o CppServer.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c gen-cpp/Calculator.cpp -o Calculator.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c gen-cpp/SharedService.cpp -o SharedService.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c gen-cpp/tutorial_constants.cpp -o tutorial_constants.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c gen-cpp/tutorial_types.cpp -o tutorial_types.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c gen-cpp/shared_constants.cpp -o shared_constants.o
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c gen-cpp/shared_types.cpp -o shared_types.o
g++ CppServer.o Calculator.o SharedService.o tutorial_constants.o tutorial_types.o shared_constants.o shared_types.o -o server -std=c++11 -lstdc++ -L/home/yaoliu/src_code/local/lib/ -lthrift
g++  -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H -I/home/yaoliu/src_code/local/include -Isrc/ -I/home/yaoliu/src_code/local/include/thrift -c src/CppClient.cpp -o CppClient.o
g++ CppClient.o Calculator.o SharedService.o tutorial_constants.o tutorial_types.o shared_constants.o shared_types.o -o client  -std=c++11 -lstdc++ -L/home/yaoliu/src_code/local/lib/ -lthrift
vchaska1@remote07:~/thrift-lab/cpp$
```

3. Run the server:
```
vchaska1@remote07:~/thrift-lab/cpp$ ./server.sh
Starting the server...
```

4. Run the client:
```
vchaska1@remote00:~/thrift-lab/cpp$ ./client.sh
ping()
1 + 1 = 2
InvalidOperation: Cannot divide by 0
15 - 10 = 5
Received log: SharedStruct(key=1, value=5)
vchaska1@remote00:~/thrift-lab/cpp$
```
___

