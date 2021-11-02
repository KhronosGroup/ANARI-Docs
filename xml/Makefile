# Copyright (c) 2013-2020 The Khronos Group Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# If a recipe fails, delete its target file. Without this cleanup, the leftover
# file from the failed recipe can falsely satisfy dependencies on subsequent
# runs of `make`.
.DELETE_ON_ERROR:

# Generator scripts and options
# GENOPTS can be e.g. '-noprotect'

PYTHON ?= python3
SCRIPTS = ../scripts
GENOPTS =

# Generate ANARI header from XML. Many other files can be generated
# from an.xml using the scripts, but they are all generated in ../ as
# part of the process of building the Specification.
#
# Targets:
#
# default / install - regenerate headers in ../include/anari/.
# validate - run XML validator on an.xml against the schema.
# test - check if anari_core.h compiles.
# clean_dirt - remove intermediate files.
# clean - remove installed and intermediate files.

INCLUDE     = ../include
TESTS	    = ../tests
ANARI	    = $(INCLUDE)/anari
SRC	    = ../src
XML	    = an.xml

# ANARI platform-specific headers - none yet
PLATFORM_HEADERS =
HEADERS     = $(ANARI)/anari_core.h $(PLATFORM_HEADERS)

default install: $(HEADERS)

################################################

# Autogenerate anari header from XML API description

# Python and XML files on which anari_core.h depends
GENSCRIPT = $(SCRIPTS)/genan.py
VKH_DEPENDS = $(XML) $(GENSCRIPT) $(SCRIPTS)/reg.py $(SCRIPTS)/generator.py

$(HEADERS): $(VKH_DEPENDS)
	$(PYTHON) $(GENSCRIPT) $(GENOPTS) -registry $(XML) -o $(ANARI) $(notdir $@)

platform: $(PLATFORM_HEADERS)

# Verify registry XML file against the schema
validate:
	jing -c registry.rnc $(XML)

# Test that anari_core.h compiles

# Platforms to test
TESTDEFS = -DVK_USE_PLATFORM_XCB_KHR -DVK_USE_PLATFORM_XLIB_KHR

test: $(HEADERS) $(ANARI)/anari.h $(ANARI)/an_platform.h
	gcc -Wall -std=c99 -c -I$(INCLUDE) -I$(TESTS) $(TESTS)/htest.c
	gcc -Wall -std=c11 -c -I$(INCLUDE) -I$(TESTS) $(TESTS)/htest.c
	g++ -Wall -c -std=c++98 -I$(INCLUDE) -I$(TESTS) $(TESTS)/htest.c
	g++ -Wall -c -std=c++11 -I$(INCLUDE) -I$(TESTS) $(TESTS)/htest.c
	rm -f $(TESTS)/test.o

################################################

# Files to clean up
PYDIRT = diag.txt dumpReg.txt errwarn.txt *.pyc regdump.txt
DIRT = $(PYDIRT) ERRS \#*

# Clean intermediate files
clean_dirt:
	-rm -f $(DIRT)

# Clean generated targets as well as intermediates.
clean clobber: clean_dirt
	-rm -f $(HEADERS) $(PLATFORM_HEADERS)
