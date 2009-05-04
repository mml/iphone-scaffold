# This was inspired by a Makefile apparently once part of Stanford's CS193P
# course.  I'm unclear what the license for that is.  Grateful for any help.

PROJECTNAME=iPhoneScaffold
APPFOLDER=$(PROJECTNAME).app

SDKVER=2.2.1
# PLATFORM=iPhoneOS
PLATFORM=iPhoneSimulator
SDK=/Developer/Platforms/$(PLATFORM).platform/Developer/SDKs/$(PLATFORM)$(SDKVER).sdk

ifeq (iPhoneSimulator,$(PLATFORM))
USERDIR=~/'Library/Application Support/iPhone Simulator/User/Applications'
else
# uh, sev?
endif

CC=/Developer/Platforms/$(PLATFORM).platform/Developer/usr/bin/gcc
CPP=/Developer/Platforms/$(PLATFORM).platform/Developer/usr/bin/g++
LD=$(CC)

# You're going to need these to do anything interesting, app-wise
LDFLAGS += -framework CoreFoundation 
LDFLAGS += -framework Foundation 
LDFLAGS += -framework UIKit 
LDFLAGS += -framework CoreGraphics

# You might be interested in one or more of these.
# LDFLAGS += -framework AddressBookUI
# LDFLAGS += -framework AddressBook
# LDFLAGS += -framework QuartzCore
# LDFLAGS += -framework GraphicsServices
# LDFLAGS += -framework CoreSurface
# LDFLAGS += -framework CoreAudio
# LDFLAGS += -framework Celestial
# LDFLAGS += -framework AudioToolbox
# LDFLAGS += -framework WebCore
# LDFLAGS += -framework WebKit
# LDFLAGS += -framework SystemConfiguration
# LDFLAGS += -framework CFNetwork
# LDFLAGS += -framework MediaPlayer
# LDFLAGS += -framework OpenGLES
# LDFLAGS += -framework OpenAL

LDFLAGS += -L"$(SDK)/usr/lib"
LDFLAGS += -F"$(SDK)/System/Library/Frameworks"
LDFLAGS += -F"$(SDK)/System/Library/PrivateFrameworks"

CFLAGS += -I"/Developer/Platforms/$(PLATFORM).platform/Developer/usr/lib/gcc/*/4.0.1/include/"
CFLAGS += -I"$(SDK)/usr/include"
CFLAGS += -I"/Developer/Platforms/$(PLATFORM).platform/Developer/usr/include/"
# CFLAGS += -I/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator$(SDKVER).sdk/usr/include
CFLAGS += -DDEBUG -std=c99
CFLAGS += -Diphoneos_version_min=$(SDKVER)
CFLAGS += -F"$(SDK)/System/Library/Frameworks"
CFLAGS += -F"$(SDK)/System/Library/PrivateFrameworks"

CPPFLAGS=$(CFLAGS)

BUILDDIR=./build/$(PLATFORM)-$(SDKVER)
SRCDIR=./Classes
RESDIR=./Resources
OBJS=$(patsubst %.m,%.o,$(wildcard $(SRCDIR)/*.m))   	# Most likely, all of your classes are Objective C
OBJS+=$(patsubst %.c,%.o,$(wildcard $(SRCDIR)/*.c))     # You might have some C files
OBJS+=$(patsubst %.cpp,%.o,$(wildcard $(SRCDIR)/*.cpp)) # C++?  Maaaaaybe
OBJS+=$(patsubst %.m,%.o,$(wildcard *.m))               # This will catch main.m
PCH=$(wildcard *.pch)
RESOURCES=$(wildcard $(RESDIR)/*)
NIBS=$(patsubst %.xib,%.nib,$(wildcard *.xib))

UUID=$(shell cat uuid)
DESTDIR=$(USERDIR)/$(UUID)
SBFILE=$(USERDIR)/$(UUID).sb

all:	$(PROJECTNAME)

$(PROJECTNAME):	$(OBJS)
	$(LD) $(LDFLAGS) -o $@ $^ 

%.o:	%.m
	$(CC) -c $(CFLAGS) $< -o $@

%.o:	%.c
	$(CC) -c $(CFLAGS) $< -o $@

%.o:	%.cpp
	$(CPP) -c $(CPPFLAGS) $< -o $@

%.nib:	%.xib
	ibtool $< --compile $@

dist:	$(PROJECTNAME) $(NIBS)
	rm -rf $(BUILDDIR)
	mkdir -p $(BUILDDIR)/$(APPFOLDER)
	cp Info.plist $(BUILDDIR)/$(APPFOLDER)/Info.plist
	echo "APPL????" > $(BUILDDIR)/$(APPFOLDER)/PkgInfo
ifneq (,$(RESOURCES))
	cp -r $(RESOURCES) $(BUILDDIR)/$(APPFOLDER)
endif
ifneq (,$(NIBS))
	mv $(NIBS) $(BUILDDIR)/$(APPFOLDER)
endif
	mv $(PROJECTNAME) $(BUILDDIR)/$(APPFOLDER)

uuid:
	uuidgen > $@

install: dist uuid
	rm -r $(DESTDIR)
	mkdir -p $(DESTDIR)/Documents $(DESTDIR)/Library/Preferences $(DESTDIR)/tmp
	cp -r $(BUILDDIR)/$(APPFOLDER) $(DESTDIR)
	echo "(version 1)\n(debug deny)\n(allow default)" > $(SBFILE)

uninstall:
	rm -r $(DESTDIR)

clean:
	rm -f $(SRCDIR)/*.o *.o
	rm -rf $(BUILDDIR)
	rm -f $(PROJECTNAME)
