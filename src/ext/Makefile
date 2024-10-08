default: all

LIB_NAME              = rdb
LIB_NAME_EXT          = $(LIB_NAME)-ext
LIB_DIR               = ../../lib
LIBRDB_SONAME_EXT     = lib$(LIB_NAME_EXT).so.${LIBRDB_VERSION}

TARGET_LIB_STATIC     = $(LIB_DIR)/lib$(LIB_NAME).a
# Artifacts:
TARGET_LIB_EXT        = $(LIB_DIR)/$(LIBRDB_SONAME_EXT)
TARGET_LIB_STATIC_EXT = $(LIB_DIR)/lib$(LIB_NAME_EXT).a

#########################################################################################
SOURCES  = $(notdir $(basename $(wildcard *.c)))
OBJECTS  = $(patsubst %,%.o,$(SOURCES))

# Source files in deps/redis directory. For now, librdb.so and librdb-ext.so,
# each will have its own copy. Take care not to pass Redis structs  from one lib
# to another!
REDIS_SOURCES = $(notdir $(basename $(wildcard ../../deps/redis/*.c)))
REDIS_OBJECTS = $(patsubst %,../../deps/redis/%.o,$(REDIS_SOURCES))

OPTIMIZATION?=-O3
LIBRDB_DEBUG?=0

STD      = -std=c99
STACK    = -fstack-protector-all -Wstack-protector
WARNS    = -Wall -Wextra -pedantic -Werror
CFLAGS   = -fPIC $(OPTIMIZATION) $(STD) $(STACK) $(WARNS) -fvisibility=hidden
DEBUG    = -g3 -DLIBRDB_DEBUG=$(LIBRDB_DEBUG)
LDFLAGS  =
LIBS     = -L $(LIB_DIR) -l $(LIB_NAME)

ifeq ($(BUILD_TLS),yes)
CFLAGS+=-DUSE_OPENSSL=1
endif

ifneq (,$(filter $(shell uname),Darwin))
    LDFLAG_SONAME = -install_name
else
    LDFLAG_SONAME = -soname
endif

######################################### RULES #######################################
all: $(TARGET_LIB_EXT) $(TARGET_LIB_STATIC_EXT)
	@echo "Done.";

$(TARGET_LIB_EXT): $(OBJECTS) $(REDIS_OBJECTS)
	$(CC) -o $@ -shared -Wl,${LDFLAG_SONAME},${LIBRDB_SONAME_EXT} ${LDFLAGS} $^  $(LIBS)

$(TARGET_LIB_STATIC_EXT): $(OBJECTS) $(REDIS_OBJECTS)
	ar rcs $@ $^

# Include object file dependencies
-include $(OBJECTS:.o=.d) $(REDIS_OBJECTS:.o=.d)

%.o: %.c
	$(CC) $(CFLAGS) -c $*.c -o $*.o $(DEBUG)
	$(CC) -MM $(CFLAGS) $*.c > $*.d

clean:
	@rm -rvf $(TARGET_LIB_EXT) $(TARGET_LIB_STATIC_EXT) ./*.o ./*.d;
