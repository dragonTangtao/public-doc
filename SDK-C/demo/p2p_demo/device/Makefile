PWD     	:= $(shell pwd)
BIN			:= lyDeviceP2PDemo
TARGET      ?= arm-linux
#TARGET      ?= arm-hisiv300-linux
#注释掉下面这行则编译linux 平台demo，否则交叉编译环境
CROSS_COMPILE   ?= $(TARGET)-
CPP     	:= $(CROSS_COMPILE)g++
CC      	:= $(CROSS_COMPILE)gcc
STRIP		:= $(CROSS_COMPILE)strip


ifeq ($(TARGET),arm-hisiv300-linux)
LIBDIR := ./lib/hisiv300
endif

ifeq ($(TARGET),arm-linux)
LIBDIR := ./lib/arm-linux
endif

ifeq ($(CROSS_COMPILE),)
LIBDIR :=./lib/linux/32bit
endif


$(BIN) : ./src/main.c ./src/h264Util.c ./src/aacUtil.c ./src/lyUtils.c
	#if use gcc,please link libstdc++
	$(CPP) -I./include -I./include/lingyang $^ -o $@ -L$(LIBDIR) -lTopvdnSDK -lCloudService -lssl -lcrypto -lpthread -lm -ldl
	#$(STRIP) $(BIN)

main.o : 
	$(CC) $(CFLAGS) main.c -o  main.o
h264Util.o : 
	$(CC) $(CFLAGS) h264Util.c -o  h264Util.o
aacUtil.o : 
	$(CC) $(CFLAGS) aacUtil.c -o  aacUtil.o
lyUtils.o : 
	$(CC) $(CFLAGS) lyUtils.c -o  lyUtils.o
	
clean:
	rm -f ./src/*.o
	rm $(BIN)
