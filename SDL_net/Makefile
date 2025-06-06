#---------------------------------------------------------------------------------
# Clear the implicit built in rules
#---------------------------------------------------------------------------------
.SUFFIXES:
#---------------------------------------------------------------------------------
ifeq ($(strip $(DEVKITPPC)),)
$(error "Please set DEVKITPPC in your environment. export DEVKITPPC=<path to>devkitPPC")
endif

include $(DEVKITPPC)/wii_rules

# Directories.
OBJ_DIR			:= obj
LIB_DIR			:= lib
BIN_DIR			:= bin
SRC_DIR			:= src
INCLUDE_DIR		:= include
INSTALL_HEADER_DIR	?= $(PORTLIBS_PATH)/wii/include
INSTALL_LIB_DIR		?= $(PORTLIBS_PATH)/wii/lib

# Tools.
PIPE_TO_SED := 2>&1 | sed "s/:\([0-9]*\):/\(\1\) :/"

# Library source files.
SRCS	:= $(wildcard $(SRC_DIR)/*.c)

# Library object files.
OBJS	:= $(subst $(SRC_DIR),$(OBJ_DIR),$(SRCS:.c=.o))

# What's a full build?
all: $(LIB_DIR)/libSDL_net.a $(INCLUDE_DIR)/SDL_net.h

# How to delete the intermediate files.
clean:
	@echo Cleaning $(LIB_DIR)/libSDL_net.a $(OBJ_DIR)
	@rm -f $(LIB_DIR)/libSDL_net.a $(OBJS) $(TEST_OBJS)
	
install: all
	@mkdir -p $(INSTALL_HEADER_DIR) $(INSTALL_LIB_DIR)
	@mkdir -p $(INSTALL_HEADER_DIR)/SDL
	@cp -frv $(LIB_DIR)/*.* $(INSTALL_LIB_DIR)
	@cp -frv include/*.* $(INSTALL_HEADER_DIR)/SDL

# How to build a library.
$(LIB_DIR)/libSDL_net.a: $(OBJS)
	@echo Archiving $@
	@-mkdir -p $(dir $@)
	@powerpc-eabi-ar crs $@ $(OBJS)
	@echo ----

# How to copy a header file
$(INCLUDE_DIR)/SDL_net.h: $(SRC_DIR)/SDL_net.h
	@echo Copy $(SRC_DIR)/SDL_net.h to $(INCLUDE_DIR)
	@-mkdir -p $(dir $@)
	@cp $(SRC_DIR)/SDL_net.h $(INCLUDE_DIR)

# Compilation flags.
COMMON_FLAGS	:= -g -O3 -mrvl -Wall $(MACHDEP)
INCLUDES		:= -Iinclude -I$(LIBOGC_INC) -I$(PORTLIBS_PATH)/wii/include/SDL -I../SDL/include
# -include sys/wait.h 
# -include sys/syslimits.h

DEFINES			:= -DGEKKO -D__WII__

CFLAGS			:= $(COMMON_FLAGS) $(INCLUDES) $(DEFINES)

# Test link flags.
LDFLAGS			:= $(COMMON_FLAGS) -L$(LIB_DIR) -L$(LIBOGC_LIB) -L$(PORTLIBS_PATH)/wii/lib -lSDL -lfat -lwiiuse -lbte -logc -lm

# How to compile C file (SDL library).
$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
	@echo Compiling $<
	@-mkdir -p $(dir $@)
	powerpc-eabi-gcc $(CFLAGS) -c $< -o $@ $(PIPE_TO_SED)
