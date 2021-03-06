################################################################################
# Makefile for Motion                                                          #
################################################################################
# Copyright 2000 by Jeroen Vreeken                                             #
#                                                                              #
# This program is published under the GNU public license version 2.0 or later. #
# Please read the file COPYING for more info.                                  #
################################################################################
# Please visit the Motion home page:                                           #
# http://www.lavrsen.dk/twiki/bin/view/Motion                                  #
################################################################################

CC      = i486-linux-gnu-gcc
INSTALL = install

################################################################################
# Install locations, controlled by setting configure flags.                    #
################################################################################
prefix      = /usr
exec_prefix = ${prefix}
bindir      = ${prefix}/bin
mandir      = ${datadir}/man
sysconfdir  = /etc/motion
datadir     = ${prefix}/share
datarootdir = ${prefix}/share
docdir      = $(datadir)/doc/motion-"3.2.11.1"
examplesdir = $(datadir)/motion-"3.2.11.1"/examples

################################################################################
# These variables contain compiler flags, object files to build and files to   #
# install.                                                                     #
################################################################################
CFLAGS       =  -Wall -g -O2 -D_REENTRANT -DHAVE_FFMPEG -I/usr/include/ffmpeg -DFFMPEG_NEW_INCLUDES -DHAVE_FFMPEG_NEW -I/usr/include/mysql -I/usr/include/postgresql -DMOTION_V4L2 -DTYPE_32BIT="int" -DHAVE_BSWAP    -Wall -DVERSION=\""3.2.11.1"\" -Dsysconfdir=\"$(sysconfdir)\" 
LDFLAGS      =  -Wl,--as-needed -L/usr/lib
LIBS         = -lm  -lpthread -ljpeg -L/usr/lib -lavformat -lavcodec -lavutil -lm -lz -L/usr/lib -lmysqlclient -lz -L/usr/lib -lpq 
VIDEO_OBJ    = video.o video2.o video_common.o
OBJ          = motion.o conf.o draw.o jpegutils.o $(VIDEO_OBJ) netcam.o \
			   netcam_ftp.o netcam_jpeg.o netcam_wget.o track.o \
			   alg.o event.o picture.o rotate.o webhttpd.o \
			   webcam.o ffmpeg.o
SRC          = $(OBJ:.o=.c)
DOC          = CHANGELOG COPYING CREDITS INSTALL README motion_guide.html
EXAMPLES     = *.conf motion.init-Debian motion.init-RH motion.init-FreeBSD.sh
PROGS        = motion
DEPEND_FILE  = .depend

################################################################################
# ALL and PROGS build Motion and, possibly, Motion-control.                    #
################################################################################
all: progs
ifneq (,$(findstring freebsd,$(VIDEO_OBJ)))
	@echo "Build complete, run \"gmake install\" to install Motion!"
else
	@echo "Build complete, run \"make install\" to install Motion!"
endif
	@echo

progs: pre-build-info $(PROGS)

################################################################################
# PRE-BUILD-INFO outputs some general info before the build process starts.    #
################################################################################
pre-build-info: 
	@echo "Welcome to the setup procedure for Motion, the motion detection daemon! If you get"
	@echo "error messages during this procedure, please report them to the mailing list. The"
	@echo "Motion Guide contains all information you should need to get Motion up and running."
	@echo "Run \"make updateguide\" to download the latest version of the Motion Guide."
	@echo
	@echo "Version: "3.2.11.1""
ifneq (,$(findstring freebsd,$(VIDEO_OBJ)))
	@echo "Platform: FreeBSD"
else
	@echo "Platform: Linux (if this is incorrect, please read README.FreeBSD)"
endif
	@echo

################################################################################
# MOTION builds motion. MOTION-OBJECTS and PRE-MOBJECT-INFO are helpers.       #
################################################################################
motion: motion-objects
	@echo "Linking Motion..."
	@echo "--------------------------------------------------------------------------------"
	$(CC) $(LDFLAGS) -o $@ $(OBJ) $(LIBS)
	@echo "--------------------------------------------------------------------------------"
	@echo "Motion has been linked."
	@echo

motion-objects: dep pre-mobject-info $(OBJ)
	@echo "--------------------------------------------------------------------------------"
	@echo "Motion object files compiled."
	@echo
	
pre-mobject-info:
	@echo "Compiling Motion object files..."
	@echo "--------------------------------------------------------------------------------"

################################################################################
# Define the compile command for C files.                                      #
################################################################################
#%.o: %.c
#	@echo -e "\tCompiling $< into $@..."
#	@$(CC) -c $(CFLAGS) $< -o $@

################################################################################
# Include the dependency file if it exists.                                    #
################################################################################
ifeq ($(DEPEND_FILE), $(wildcard $(DEPEND_FILE)))
ifeq (,$(findstring clean,$(MAKECMDGOALS)))
-include $(DEPEND_FILE)
endif
endif

################################################################################
# Make the dependency file depend on all header files and all relevant source  #
# files. This forces the file to be re-generated if the source/header files    #
# change. Note, however, that the existing version will be included before     #
# re-generation.                                                               #
################################################################################
$(DEPEND_FILE): *.h $(SRC)
	@echo "Generating dependencies, please wait..."
	@$(CC) $(CFLAGS) -M $(SRC) > .tmp
	@mv -f .tmp $(DEPEND_FILE)
	@echo

################################################################################
# DEP, DEPEND and FASTDEP generate the dependency file.                        #
################################################################################
dep depend fastdep: $(DEPEND_FILE)

################################################################################
# INSTALL installs all relevant files.                                         #
################################################################################
install:
	@echo "Installing files..."
	@echo "--------------------------------------------------------------------------------"
	mkdir -p $(DESTDIR)$(bindir)
	mkdir -p $(DESTDIR)$(mandir)/man1
	mkdir -p $(DESTDIR)$(sysconfdir)
	mkdir -p $(DESTDIR)$(docdir)
	mkdir -p $(DESTDIR)$(examplesdir)
	$(INSTALL) motion.1 $(DESTDIR)$(mandir)/man1
	$(INSTALL) $(DOC) $(DESTDIR)$(docdir)
	$(INSTALL) $(EXAMPLES) $(DESTDIR)$(examplesdir)
	$(INSTALL) motion-dist.conf $(DESTDIR)$(sysconfdir)
	for prog in $(PROGS); \
	do \
	($(INSTALL) $$prog $(DESTDIR)$(bindir) ); \
	done
	@echo "--------------------------------------------------------------------------------"
	@echo "Install complete! The default configuration file, motion-dist.conf, has been"
	@echo "installed to $(sysconfdir). You need to rename/copy it to $(sysconfdir)/motion.conf"
	@echo "for Motion to find it. More configuration examples as well as init scripts"
	@echo "can be found in $(examplesdir)."
	@echo

################################################################################
# UNINSTALL and REMOVE uninstall already installed files.                      #
################################################################################
uninstall remove: pre-build-info
	@echo "Uninstalling files..."
	@echo "--------------------------------------------------------------------------------"
	for prog in $(PROGS); \
	do \
		($ rm -f $(bindir)/$$prog ); \
	done
	rm -f $(mandir)/man1/motion.1
	rm -f $(sysconfdir)/motion-dist.conf
	rm -rf $(docdir)
	rm -rf $(examplesdir)
	@echo "--------------------------------------------------------------------------------"
	@echo "Uninstall complete!"
	@echo

################################################################################
# CLEAN is basic cleaning; removes object files and executables, but does not  #
# remove files generated from the configure step.                              #
################################################################################
clean: pre-build-info
	@echo "Removing compiled files and binaries..."
	@rm -f *~ *.jpg *.o $(PROGS) combine $(DEPEND_FILE)

################################################################################
# DIST restores the directory to distribution state.                           #
################################################################################
dist: distclean updateguide
	@chmod -R 644 *
	@chmod 755 configure

################################################################################
# DISTCLEAN removes all files generated during the configure step in addition  #
# to basic cleaning.                                                           #
################################################################################
distclean: clean
	@echo "Removing files generated by configure..."
	@rm -f config.status config.log config.cache Makefile motion.init-RH motion.init-Debian motion.init-FreeBSD.sh
	@rm -f thread1.conf thread2.conf thread3.conf thread4.conf motion-dist.conf motion-help.conf motion.spec
	@rm -rf autom4te.cache config.h
	@echo "You will need to re-run configure if you want to build Motion."
	@echo

################################################################################
# UPDATEGUIDE downloads the Motion Guide from TWiki.                           #
################################################################################
updateguide: pre-build-info
	@echo "Downloading Motion Guide. If it fails, please check your Internet connection."
	@echo
	wget www.lavrsen.dk/twiki/bin/view/Motion/MotionGuideOneLargeDocument?skin=text -O motion_guide.tmp
	@echo "Cleaning up and fixing links..."
	@cat motion_guide.tmp | sed -e 's/\?skin=text//g;s,"/twiki/,"http://www.lavrsen.dk/twiki/,g' > motion_guide.html
	@rm -f motion_guide.tmp
	@echo "All done, you should now have an up-to-date local copy of the Motion guide."
	@echo
