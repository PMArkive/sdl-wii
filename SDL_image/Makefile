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
TEST_OBJ_DIR		:= $(OBJ_DIR)/test
TEST_SRC_DIR		:= src
INSTALL_HEADER_DIR	?= $(PORTLIBS_PATH)/wii/include
INSTALL_LIB_DIR		?= $(PORTLIBS_PATH)/wii/lib

# Tools.
PIPE_TO_SED := 2>&1 | sed "s/:\([0-9]*\):/\(\1\) :/"

# Library source files.
SRCS	:= $(filter-out $(SRC_DIR)/IMG_ImageIO.c $(SRC_DIR)/showimage.c, $(wildcard $(SRC_DIR)/*.c))

# Library object files.
OBJS	:= $(subst $(SRC_DIR),$(OBJ_DIR),$(SRCS:.c=.o))

# Test source files.
# It can be useful to switch this variable around to select individual tests which are problematic.
TEST_SRCS	:= $(TEST_SRC_DIR)/showimage.c

# Test object files.
TEST_OBJS	:= $(subst $(TEST_SRC_DIR)/,$(TEST_OBJ_DIR)/,$(TEST_SRCS:.c=.o))

# Test .DOL files.
TEST_DOLS	:= $(subst $(TEST_OBJ_DIR),$(BIN_DIR),$(TEST_OBJS:.o=.dol))

# What's a full build?
all: $(LIB_DIR)/libSDL_image.a $(INCLUDE_DIR)/SDL_image.h

# How to delete the intermediate files.
clean:
	@echo Cleaning $(LIB_DIR)/libSDL_image.a $(OBJ_DIR)
	@rm -f $(LIB_DIR)/libSDL_image.a $(OBJS) $(TEST_OBJS)
	
install: all
	@mkdir -p $(INSTALL_HEADER_DIR) $(INSTALL_LIB_DIR)
	@mkdir -p $(INSTALL_HEADER_DIR)/SDL
	@cp -frv $(LIB_DIR)/*.* $(INSTALL_LIB_DIR)
	@cp -frv include/*.* $(INSTALL_HEADER_DIR)/SDL

# How to install to SD card ready for running.
test-install: $(TEST_DOLS)
	@-mkdir -p dols
	cp $(TEST_DOLS) dols

# How to build a library.
$(LIB_DIR)/libSDL_image.a: $(OBJS)
	@echo Archiving $@
	@-mkdir -p $(dir $@)
	@powerpc-eabi-ar crs $@ $(OBJS)
	@echo ----

# How to build a DOL.
$(BIN_DIR)/%.dol: $(TEST_OBJ_DIR)/%.elf
	@echo Creating DOL $@
	@-mkdir -p $(dir $@)
	#powerpc-eabi-objcopy -O binary $< $@
	elf2dol $< $@
	@echo ----

# How to copy the header file
$(INCLUDE_DIR)/SDL_image.h: $(SRC_DIR)/SDL_image.h
	@echo Copy $(SRC_DIR)/SDL_image.h to $(INCLUDE_DIR)
	@-mkdir -p $(dir $@)
	@cp $(SRC_DIR)/SDL_image.h $(INCLUDE_DIR)

# Compilation flags.
COMMON_FLAGS	:= -g -O3 -mrvl -Wall -std=gnu89 $(MACHDEP)
INCLUDES		:= -Iinclude -I$(LIBOGC_INC) -I$(PORTLIBS_PATH)/wii/include -I$(LIBOGC_INC)/jpeg -I../SDL/include
# -I$(DEVKITPRO)/libpng/include
DEFINES			:= -DGEKKO -D__WII__ -DDEBUG_ERROR -DDEBUG_TIMERS -DDEBUG_THREADS -DDEBUG_BUILD -DDEBUG_CONVERT -DLOAD_BMP -DLOAD_JPG -DLOAD_GIF -DLOAD_LBM -DLOAD_PCX -DLOAD_PNM -DLOAD_TGA -DLOAD_XCF -DLOAD_XPM -DLOAD_XV -DLOAD_PNG
# 
# -DLOAD_TIFF
CFLAGS			:= $(foreach dir,$(PORTLIBS),-L$(dir)/lib) $(foreach dir,$(PORTLIBS),-I$(dir)/include) $(COMMON_FLAGS) $(INCLUDES) $(DEFINES)

# Test link flags.
LDFLAGS			:= $(COMMON_FLAGS) -L$(LIB_DIR) -L$(LIBOGC_LIB) -L$(PORTLIBS_PATH)/wii/lib -lSDL_image -lSDL -lpng -ljpeg -lfat -lwiiuse -lbte -logc -lm  -lz
# -ltiff

# How to link an ELF.
$(TEST_OBJ_DIR)/%.elf: $(TEST_OBJ_DIR)/%.o $(LIB_DIR)/libSDL_image.a $(DEVKITPRO)/libSDL/lib/libSDL.a 
	@echo Linking $@
	@-mkdir -p $(dir $@)
	powerpc-eabi-g++ -o $@ $< $(LDFLAGS)
	#keep elf for debugging
	#cp $@ /tmp/elf 

# How to compile C file (Tests).
$(TEST_OBJ_DIR)/%.o: $(TEST_SRC_DIR)/showimage.c
	@echo Compiling $<
	@-mkdir -p $(dir $@)
	powerpc-eabi-gcc $(CFLAGS) -c $< -o $@ $(PIPE_TO_SED)

# How to compile C file (SDL library).
$(OBJ_DIR)/%.o: $(SRC_DIR)/%.c
	@echo Compiling $<
	@-mkdir -p $(dir $@)
	powerpc-eabi-gcc $(CFLAGS) -c $< -o $@ $(PIPE_TO_SED)
