# -*-makefile-*-
#
# use project_2005815 for allas storage
# --> HPLT project (100TiB storage available)
#


include Makefile.def
include ../Makefile.def

.PHONY: all
all:
	${MAKE} -C raw all
	${MAKE} release


## standard procedures are specified in the following makefiles
##
##   Makefile.submit ....... submit a job to our cluster
##   Makfile.release ....... download packages and website

include ../Makefile.submit
include ../Makefile.release

