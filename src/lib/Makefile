default: all

LIB_NAME              = rdb
LIB_DIR               = ../../lib
LIBRDB_SONAME         = lib$(LIB_NAME).so.${LIBRDB_VERSION}

# Artifacts:
TARGET_LIB            = $(LIB_DIR)/${LIBRDB_SONAME}
TARGET_LIB_STATIC     = $(LIB_DIR)/lib$(LIB_NAME).a

# Source files in the working directory
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
DEBUG    = -g3
LIBS     =

ifeq ($(LIBRDB_DEBUG), 1)
	CFLAGS += -DLIBRDB_DEBUG=1
else
	CFLAGS += -DNDEBUG=1
endif

ifneq (,$(filter $(shell uname),Darwin))
    LDFLAG_SONAME = -install_name
else
    LDFLAG_SONAME = -soname
endif

######################################### RULES #######################################
all: $(TARGET_LIB) $(TARGET_LIB_STATIC)
	@echo "Done.";

$(TARGET_LIB): $(OBJECTS) $(REDIS_OBJECTS)
	$(CC) -o $@ -shared -Wl,${LDFLAG_SONAME},${LIBRDB_SONAME} ${LDFLAGS} $^

$(TARGET_LIB_STATIC): $(OBJECTS) $(REDIS_OBJECTS)
	ar rcs $@ $^

# Include object file dependencies
-include $(OBJECTS:.o=.d) $(REDIS_OBJECTS:.o=.d)

# Compile source files in the working directory to object files
%.o: %.c
	$(CC) -fPIC $(CFLAGS) -c $*.c -o $*.o $(DEBUG) $(LIBS)
	$(CC) -MM $(CFLAGS) $*.c > $*.d

clean:
	@rm -rvf $(TARGET_LIB) $(TARGET_LIB_STATIC) ./*.o ./*.d;

.PHONY: all clean
