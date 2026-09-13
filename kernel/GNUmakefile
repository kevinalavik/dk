# Nuke built-in rules.
.SUFFIXES:

# Delete the target of a failed recipe.
.DELETE_ON_ERROR:

# This is the name that our final executable will have.
# Change as needed.
override OUTPUT := kernel

# Install prefix; /usr/local is a good, standard default pick.
PREFIX := /usr/local

# User controllable dc compiler command.
DCC := dcc

# User controllable linker command.
LD := ld

# User controllable DC flags.
DCFLAGS := -g -O2

# User controllable linker flags. We set none by default.
LDFLAGS :=

# Internal DC flags that should not be changed by the user.
override DCFLAGS += \
    -target x86_64-elf \
    -mcmodel kernel \
    -fno-red-zone \
    -fno-simd \
    -fno-x87 \
    -fno-stack-protector 

# Internal linker flags that should not be changed by the user.
override LDFLAGS += \
    -m elf_x86_64 \
    -nostdlib \
    -static \
    -z max-page-size=0x1000 \
    -z noexecstack \
    --gc-sections \
    -T linker-scripts/x86_64.lds

# Check if the linker supports -no-pie and enable it if it does.
override LD_HAS_NO_PIE := $(shell ! $(LD) --help 2>/dev/null | grep -qE '(^|[[:space:]])--?no-pie([[:space:]]|$$)'; echo $$?)

ifeq ($(LD_HAS_NO_PIE),1)
    override LDFLAGS += \
        -no-pie
endif

# Use "find" to glob all *.dc files in the tree and obtain the object and
# module dependency file names.
override DCFILES := $(shell find -L src -type f -name '*.dc' 2>/dev/null | LC_ALL=C sort)
override OBJ := $(addprefix obj/,$(DCFILES:.dc=.dc.o))
override MODULE_DEPS := $(addprefix obj/,$(DCFILES:.dc=.dc.d))

# Default target. This must come first, before module dependencies.
.PHONY: all
all: bin/$(OUTPUT)

# Include module dependencies.
-include $(MODULE_DEPS)

# Link rules for the final executable.
bin/$(OUTPUT): GNUmakefile linker-scripts/x86_64.lds $(OBJ)
	mkdir -p "$(dir $@)"
	$(LD) $(LDFLAGS) $(OBJ) -o $@

# Compilation rules for *.dc files.
obj/%.dc.o: %.dc GNUmakefile
	mkdir -p "$(dir $@)"
	$(DCC) $(DCFLAGS) -I src -c $< -o $@ --depfile $(@:.o=.d)

# Remove object files and the final executable.
.PHONY: clean
clean:
	rm -rf bin obj

# Remove everything built and generated.
.PHONY: distclean
distclean: clean
	rm -rf .cache compile_commands.json

# Install the final built executable to its final on-root location.
.PHONY: install
install: all
	install -d "$(DESTDIR)$(PREFIX)/share/$(OUTPUT)"
	install -m 644 bin/$(OUTPUT) "$(DESTDIR)$(PREFIX)/share/$(OUTPUT)/"

# Try to undo whatever the "install" target did.
.PHONY: uninstall
uninstall:
	rm -f "$(DESTDIR)$(PREFIX)/share/$(OUTPUT)/$(OUTPUT)"
	-rmdir "$(DESTDIR)$(PREFIX)/share/$(OUTPUT)"
