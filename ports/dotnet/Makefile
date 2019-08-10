include ../../py/mkenv.mk

CROSS = 0

# qstr definitions (must come before including py.mk)
QSTR_DEFS = qstrdefsport.h

# include py core make definitions
include $(TOP)/py/py.mk

ifeq ($(CROSS), 1)
CROSS_COMPILE = arm-none-eabi-
endif

INC += -I.
INC += -I$(TOP)
INC += -I$(BUILD)

IRIL = dotnet run --project /Users/fak/Dropbox/Projects/Iril/Cli/Cli.csproj --

CC = gcc
LD = gcc

CFLAGS = $(INC) -Wall -Werror -std=c99 $(COPT)
LDFLAGS = -Wl

# Tune for Debugging or Optimization
ifeq ($(DEBUG), 1)
CFLAGS += -O0 -ggdb
else
CFLAGS += -Os -DNDEBUG
endif

LIBS =

SRC_C = \
	main.c \
	uart_core.c \
	lib/utils/printf.c \
	lib/utils/stdout_helpers.c \
	lib/utils/pyexec.c \
	lib/mp-readline/readline.c \
	supervisor/stub/safe_mode.c \
	supervisor/stub/serial.c \
	supervisor/stub/stack.c \
	supervisor/shared/translate.c \
	$(BUILD)/_frozen_mpy.c \

OBJ = $(PY_CORE_O) $(addprefix $(BUILD)/, $(SRC_C:.c=.o))
ALL_C = $(PY_CORE_O_BASENAME:.o=.c) $(SRC_C)

all: $(BUILD)/Python.dll

$(BUILD)/_frozen_mpy.c: frozentest.mpy $(BUILD)/genhdr/qstrdefs.generated.h
	$(ECHO) "MISC freezing bytecode"
	$(Q)$(TOP)/tools/mpy-tool.py -f -q $(BUILD)/genhdr/qstrdefs.preprocessed.h -mlongint-impl=none $< > $@

$(BUILD)/Python.dll: $(ALL_C)
	$(ECHO) "COMPILE $@"
	$(Q)$(IRIL) $(CFLAGS) -o $@ $^ $(LIBS)
	$(Q)$(SIZE) $@

include $(TOP)/py/mkrules.mk
