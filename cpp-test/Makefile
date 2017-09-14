GEN_SRC := Calculator.cpp SharedService.cpp tutorial_constants.cpp tutorial_types.cpp shared_constants.cpp shared_types.cpp
GEN_OBJ := $(patsubst %.cpp,%.o, $(GEN_SRC))

THRIFT_DIR := /home/yaoliu/src_code/local/include
LIB_DIR := /home/yaoliu/src_code/local/lib/
#THRIFT_DIR=/home/ybai4/usr/local/include
#LIB_DIR=/home/ybai4/usr/local/lib

INC := -I$(THRIFT_DIR) -Isrc/ -I$(THRIFT_DIR)/thrift 

.PHONY: all clean

all: server client

%.o: gen-cpp/%.cpp
	$(CXX) -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H $(INC) -c $< -o $@

%.o: src/%.cpp
	$(CXX) -std=c++11 -lstdc++ -Wall -DHAVE_INTTYPES_H -DHAVE_NETINET_IN_H $(INC) -c $< -o $@

server: CppServer.o $(GEN_OBJ)
	$(CXX) $^ -o $@ -std=c++11 -lstdc++ -L$(LIB_DIR) -lthrift 

client: CppClient.o $(GEN_OBJ)
	$(CXX) $^ -o $@ -std=c++11 -lstdc++ -L$(LIB_DIR) -lthrift 

clean:
	$(RM) *.o server client
