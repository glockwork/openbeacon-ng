CXX     :=g++
TARGET  :=openbeacon-power
SOURCES :=src/bmMapHandleToItem.cpp src/main.cpp src/crypto.cpp
LIBS    :=-lm

# determine program version
PROGRAM_VERSION:=$(shell git describe --tags --abbrev=4 --dirty 2>/dev/null | sed s/^v//)
ifeq ("$(PROGRAM_VERSION)","")
    PROGRAM_VERSION:='unknown'
endif

# determine if we have custom encryption keys
ifeq ("$(wildcard src/custom-encryption-keys.h)","")
    ENCRYPTION_KEYS:=
else
    ENCRYPTION_KEYS:=-DCUSTOM_ENCRYPTION_KEYS
endif

DEBUG   :=-O3
CXXOPT  :=$(DEBUG)
INCLUDE :=-I../../firmware/nRF51/tag-power/inc/
CXXFLAGS:=-Werror -Wall -D_REENTRANT -DPROGRAM_VERSION=\"$(PROGRAM_VERSION)\" -DPROGRAM_NAME=\"$(TARGET)\" $(ENCRYPTION_KEYS) $(INCLUDE)
LDFLAGS :=$(LIBS)
OBJS    :=$(SOURCES:%.cpp=%.o)
LOGJSON :=$(LOGFILE:%.bin=%.json.bz2)
PREFIX  :=/usr/local

all: $(TARGET)

.PHONY: all version run install indent debug dependencies-fedora cleanall clean

version:
	@echo "$(TARGET) version $(PROGRAM_VERSION)"

run: $(TARGET)
	./$(TARGET)

install: $(TARGET)
	install $(TARGET) $(PREFIX)/bin/

indent: $(SOURCES)
	find src -iname '*\.[cph]*' -exec indent -c81 -i4 -cli4 -bli0 -ts 4 \{\} \;
	rm -f src/*~

$(TARGET): .depend $(OBJS)
	$(CXX) $(LDOPT) $(OBJS) $(LDFLAGS) -o $@

.depend: $(SOURCES)
	$(CXX) $(CXXFLAGS) $(CXXOPT) -MM $^ > $@

.cpp.o:
	$(CXX) $(CXXFLAGS) $(CXXOPT) -c $< -o $@

dependencies-fedora:
	sudo yum install gcc-c++

cleanall: clean
	rm -f .depend

clean:
	rm -f $(TARGET) $(OBJS) *~

include .depend
