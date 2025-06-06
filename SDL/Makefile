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
OBJ_DIR			:= obj/wii
LIB_DIR			:= lib
BIN_DIR			:= bin/wii
SDL_OBJ_DIR		:= $(OBJ_DIR)/sdl
SDL_SRC_DIR		:= src
INSTALL_HEADER_DIR	?= $(PORTLIBS_PATH)/wii/include
INSTALL_LIB_DIR		?= $(PORTLIBS_PATH)/wii/lib

# Tools.
PIPE_TO_SED := 2>&1 | sed "s/:\([0-9]*\):/\(\1\) :/"

# Library source files.
SDL_SRCS	:= \
	$(wildcard $(SDL_SRC_DIR)/*.c) \
	$(wildcard $(SDL_SRC_DIR)/audio/*.c) \
	$(wildcard $(SDL_SRC_DIR)/audio/dummy/*.c) \
	$(wildcard $(SDL_SRC_DIR)/audio/wii/*.c) \
	$(wildcard $(SDL_SRC_DIR)/cdrom/*.c) \
	$(wildcard $(SDL_SRC_DIR)/cdrom/dummy/*.c) \
	$(wildcard $(SDL_SRC_DIR)/cpuinfo/*.c) \
	$(wildcard $(SDL_SRC_DIR)/events/*.c) \
	$(wildcard $(SDL_SRC_DIR)/file/*.c) \
	$(wildcard $(SDL_SRC_DIR)/file/wii/*.c) \
	$(wildcard $(SDL_SRC_DIR)/joystick/*.c) \
	$(wildcard $(SDL_SRC_DIR)/joystick/wii/*.c) \
	$(wildcard $(SDL_SRC_DIR)/loadso/dummy/*.c) \
	$(wildcard $(SDL_SRC_DIR)/main/wii/*.c) \
	$(wildcard $(SDL_SRC_DIR)/stdlib/*.c) \
	$(wildcard $(SDL_SRC_DIR)/thread/*.c) \
	$(wildcard $(SDL_SRC_DIR)/thread/wii/*.c) \
	$(wildcard $(SDL_SRC_DIR)/timer/*.c) \
	$(wildcard $(SDL_SRC_DIR)/timer/wii/*.c) \
	$(wildcard $(SDL_SRC_DIR)/video/*.c) \
	$(wildcard $(SDL_SRC_DIR)/video/dummy/*.c) \
	$(wildcard $(SDL_SRC_DIR)/video/wii/*.c)

# Library object files.
SDL_OBJS	:= $(subst $(SDL_SRC_DIR),$(SDL_OBJ_DIR),$(SDL_SRCS:.c=.o))

# What's a full build?
all: $(LIB_DIR)/libSDL.a

# How to delete the intermediate files.
clean:
	@echo Cleaning $(LIB_DIR) $(SDL_OBJS)
	@rm -fr $(LIB_DIR) $(SDL_OBJS)

install: all
	@mkdir -p $(INSTALL_HEADER_DIR) $(INSTALL_LIB_DIR)
	@mkdir -p $(INSTALL_HEADER_DIR)/SDL
	@cp -frv $(LIB_DIR)/*.* $(INSTALL_LIB_DIR)
	@cp -frv include/*.* $(INSTALL_HEADER_DIR)/SDL

# Used to install library by pressing Alt+R in Programmer's Notepad
run: all install
	
test-make:
	$(MAKE) -f Makefile.test install

test-clean: 
	$(MAKE) -f Makefile.test clean

# How to build a library.
$(LIB_DIR)/libSDL.a: $(SDL_OBJS)
	@echo Archiving $@
	@-mkdir -p $(dir $@)
	powerpc-eabi-ar crs $@ $(SDL_OBJS)
	@echo ----

# How to build a DOL.
$(BIN_DIR)/%.dol: $(TEST_OBJ_DIR)/%.elf
	@echo Creating DOL $@
	@-mkdir -p $(dir $@)
	elf2dol $< $@
	@echo ----

# Compilation flags.
COMMON_FLAGS	:= -g -O3 -Wall $(MACHDEP) 
INCLUDES		:= -Iinclude -I$(LIBOGC_INC)
DEFINES			:= -DGEKKO -D__WII__
CFLAGS			:= $(COMMON_FLAGS) $(INCLUDES) $(DEFINES)

# Test link flags.
LDFLAGS			:= $(COMMON_FLAGS) -L$(LIB_DIR) -L$(LIBOGC_LIB) $(PORTLIBS_PATH)/wii/lib -lSDL -lwiikeyboard -lfat -lwiiuse -lbte -logc -lm 

# How to compile C file (SDL library).
$(SDL_OBJ_DIR)/%.o: $(SDL_SRC_DIR)/%.c
	@echo Compiling $<
	@-mkdir -p $(dir $@)
	powerpc-eabi-gcc $(CFLAGS) -c $< -o $@ $(PIPE_TO_SED)
