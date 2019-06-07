######################################
# target
######################################
TARGET = SlamySTM32Floppy

# Librerie et al.
include Makefile.common

include Makefile.board

######################################
# source
######################################
# C sources
C_SOURCES =  \
	main.c \
	libcsupport.c \
	floppy_control.c \
	floppy_crc.c \
	floppy_flux.c \
	floppy_flux_read.c \
	floppy_flux_write.c \
	floppy_gcr_read.c \
	floppy_indexSim.c \
	floppy_mfm.c \
	floppy_mfm_read.c \
	floppy_sector.c \
	floppy_sector_amiga.c \
	floppy_sector_c64.c \
	floppy_sector_iso.c \
	floppy_sector_raw.c \
	floppy_sector_raw_syncFinder.c \
	floppy_settings.c \
	

C_SOURCES += \
	$(LIBRARY_C_SOURCES) \


# ASM sources
ASM_SOURCES =  \
	CMSIS/startup_stm32f4xx.s \

# C includes
C_INCLUDES =  \
	-IUtilities \


C_INCLUDES += \
	$(LIBRARY_C_INCLUDES) \


# compile gcc flags
ASFLAGS = $(MCU) $(AS_DEFS) $(AS_INCLUDES) $(OPT) -Wall -fdata-sections -ffunction-sections

CFLAGS = $(MCU) $(C_DEFS) $(C_INCLUDES) $(OPT) -Wall -fdata-sections -ffunction-sections

ifeq ($(DEBUG), 1)
	CFLAGS += -g -gdwarf-2 -O0 -pg

	ASM_SOURCES += \
		src/profile_mcount_arm.s \


endif


# Generate dependency information
CFLAGS += -MMD -MP -MF"$(@:%.o=%.d)"


#######################################
# LDFLAGS
#######################################
# link script
LDSCRIPT = \
	stm32_flash.ld \


# libraries
LIBS = -lc -lm -lnosys
LIBDIR =
LDFLAGS = $(MCU) -specs=nano.specs -T$(LDSCRIPT) $(LIBDIR) $(LIBS) -Wl,-Map=$(BUILD_DIR)/$(TARGET).map,--cref -Wl,--gc-sections

# default action: build all
all: $(BUILD_DIR)/$(TARGET).elf $(BUILD_DIR)/$(TARGET).hex $(BUILD_DIR)/$(TARGET).bin


#######################################
# build the application
#######################################
# list of objects
OBJECTS = $(addprefix $(BUILD_DIR)/,$(notdir $(C_SOURCES:.c=.o)))
vpath %.c $(sort $(dir $(C_SOURCES)))
# list of ASM program objects
OBJECTS += $(addprefix $(BUILD_DIR)/,$(notdir $(ASM_SOURCES:.s=.o)))
vpath %.s $(sort $(dir $(ASM_SOURCES)))

$(BUILD_DIR)/%.o: %.c Makefile | $(BUILD_DIR)
	$(CC) -c $(CFLAGS) -Wa,-a,-ad,-alms=$(BUILD_DIR)/$(notdir $(<:.c=.lst)) $< -o $@

$(BUILD_DIR)/%.o: %.s Makefile | $(BUILD_DIR)
	$(AS) -c $(CFLAGS) $< -o $@

$(BUILD_DIR)/$(TARGET).elf: $(OBJECTS) Makefile
	$(CC) $(OBJECTS) $(LDFLAGS) -o $@
	$(SZ) $@

$(BUILD_DIR)/%.hex: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(HEX) $< $@

$(BUILD_DIR)/%.bin: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(BIN) $< $@

$(BUILD_DIR):
	mkdir $@

#######################################
# clean up
#######################################
clean:
	-rm -fR $(BUILD_DIR)

#######################################
# dependencies
#######################################
-include $(wildcard $(BUILD_DIR)/*.d)

# *** EOF ***
