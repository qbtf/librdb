default: all

LIB_NAME              = rdb
LIB_DIR               = ../lib
LIB_NAME_EXT          = $(LIB_NAME)-ext

# Artifacts:
TARGET_TEST           = example1

#########################################################################################
SOURCES  = $(notdir $(basename $(wildcard *.c)))
OBJECTS  = $(patsubst %,%.o,$(SOURCES))
TARGETS  = $(basename $(SOURCES))

OPTIMIZATION?=-O3

STD         = -std=c99
STACK       = -fstack-protector-all -Wstack-protector
WARNS       = -Wall -Wextra -pedantic -Werror
CFLAGS      = -fPIC $(OPTIMIZATION) $(STD) $(STACK) $(WARNS)
DEBUG       = -g3 -DDEBUG=1
LIBS        = -L /usr/lib -L $(LIB_DIR) -l $(LIB_NAME) -l $(LIB_NAME_EXT)

ifeq ($(BUILD_TLS),yes)
CFLAGS+=-DUSE_OPENSSL=1
LIBS+=-lssl -lcrypto
endif

######################################### RULES #######################################
all: $(TARGET_TEST)
	@echo "Done.";

$(TARGET_TEST): %: %.c
	$(CC) $(CFLAGS) -o $@ $<  $(DEBUG) $(LIBS)

clean:
	@rm -rvf $(TARGETS) ./*.o ./*.dSYM;

.PHONY: all clean