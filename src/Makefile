UNAME := $(shell uname -s)
CXX=g++
CC=gcc
AR=ar
PLATFORM=`uname`
ARCH=`uname -m`
#OPTFLAGS=-g -O0
OPTFLAGS=-O3 -DBOOST_UBLAS_NDEBUG -DNDEBUG
CXXFLAGS=-Wall -I/usr/local/include/boost_1_46_1 -fPIC $(OPTFLAGS)
CFLAGS=-Wall -fPIC $(OPTFLAGS)
ifeq ($(UNAME),Darwin)
	CXXFLAGS+=-arch i386
	CFLAGS+=-arch i386
	LDFLAGS=-arch i386 -L/usr/local/lib -lz -lpthread $(OPTFLAGS)
endif

ifeq ($(UNAME),MINGW32_NT-6.1)
	LDFLAGS=-L/usr/local/lib $(OPTFLAGS)
endif

ifeq ($(UNAME),Linux)
	LDFLAGS=-L/usr/local/lib libtag-$(PLATFORM)-$(ARCH).a -lz -lpthread $(OPTFLAGS)
endif

MODULES_LIB = \
    AudioBufferInput.o \
    AudioStreamInput.o \
    Base64.o \
    Codegen.o \
    Fingerprint.o \
    MatrixUtility.o \
    SubbandAnalysis.o
MODULES = $(MODULES_LIB)

main: $(MODULES) main.o
	$(CXX) $(MODULES) $(LDFLAGS) main.o -o ../codegen.$(PLATFORM)-$(ARCH)
ifeq ($(UNAME),Darwin)
	$(CXX) -arch i386 -shared -fPIC -o libcodegen.$(PLATFORM)-$(ARCH).so $(MODULES_LIB) -lz -framework Accelerate -framework vecLib
	libtool -dynamic -flat_namespace -install_name libcodegen.4.0.0.dylib -lSystem -compatibility_version 4.0 -macosx_version_min 10.6 \
	    -current_version 4.0.0 -o libcodegen.4.0.0.dylib -undefined suppress \
	    $(MODULES) -framework vecLib -framework Accelerate
endif

ifeq ($(UNAME),MINGW32_NT-6.1)
	$(CXX) -shared -fPIC -o codegen.dll $(MODULES_LIB)
	$(AR) rc codegen.lib $(MODULES_LIB)
endif

ifeq ($(UNAME),Linux)
	$(CXX) -shared -fPIC -o libcodegen.$(PLATFORM)-$(ARCH).so $(MODULES_LIB) 
endif

%.o: %.c %.h
	$(CC) $(CFLAGS) -c -o $@ $<

%.o: %.c
	$(CC) $(CFLAGS) -c -o $@ $<

%.o: %.cxx %.h
	$(CXX) $(CXXFLAGS) -c -o $@ $<

%.o: %.cxx
	$(CXX) $(CXXFLAGS) -c -o $@ $<

clean:
	rm -f *.o ../codegen.$(UNAME)-$(ARCH)
	rm -f *.so
ifeq ($(UNAME),Darwin)
	rm -f *.dylib
endif
