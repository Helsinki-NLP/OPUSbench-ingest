# -*-makefile-*-
#
# use project_2005815 for allas storage
# --> HPLT project (100TiB storage available)
#


include Makefile.def
include ../Makefile.def



.PHONY: all
all:
	${MAKE} multi-linksets-0.9
	${MAKE} algfiles
	${MAKE} -C raw all
	${MAKE} release


## standard procedures are specified in the following makefiles
##
##   Makefile.submit ....... submit a job to our cluster
##   Makfile.release ....... download packages and website

include ../Makefile.submit
include ../Makefile.release

MULTISET_DIR = multi-linksets-0.9/2024/${LANGSTR}

multi-linksets-0.9:
	wget https://raw.githubusercontent.com/Helsinki-NLP/OpenSubtitles-devtest/refs/heads/master/devtest-xml/multi-linksets-0.9/2024/${LANGSTR}.zip
	unzip -P OpenSubtitles2024-multiset ${LANGSTR}.zip


DOCNAMES := $(sort $(notdir $(shell find ${MULTISET_DIR}/ -maxdepth 1 -type d)))
DOCLINKS := $(foreach doc,${DOCNAMES},$(foreach lang,${LANGUAGES},${doc}.${lang}))
ALGFILES := $(patsubst %,${CORPUSXML}/%.xml,${LANGPAIRS})

.INTERMEDIATE: ${DOCLINKS}

.PHONY: algfiles
algfiles: ${ALGFILES}

${ALGFILES}: ${DOCLINKS}
	mkdir -p $(dir $@)
	echo '<?xml version="1.0" encoding="utf-8"?>'                        > $(@:.gz=)
	echo '<!DOCTYPE cesAlign PUBLIC "-//CES//DTD XML cesAlign//EN" "">' >> $(@:.gz=)
	echo '<cesAlign version="1.0">'                                     >> $(@:.gz=)
	for d in ${DOCNAMES}; do \
	  s=$(firstword $(subst -, ,$(basename $(notdir $@)))); \
	  t=$(lastword $(subst -, ,$(basename $(notdir $@)))); \
	  S=`head -1 $$d.$$s`; \
	  T=`head -1 $$d.$$t`; \
	  echo "<linkGrp targType=\"s\" fromDoc=\"$$S\" toDoc=\"$$T\">"     >> $(@:.gz=); \
	  paste $$d.$$s $$d.$$t -d\; | tail -n +2 | \
	  sed 's/^/<link xtargets="/' | sed 's/$$/" \/>/'                   >> $(@:.gz=); \
	  echo '</linkGrp>'                                                 >> $(@:.gz=); \
	done
	echo '</cesAlign>' >> $(@:.gz=)
	gzip -f $(@:.gz=)

${DOCLINKS}:
	echo "${MULTISET_DIR}/$(basename $@)/$(patsubst .%,%,$(suffix $@)).xml"
	if [ $(words $(wildcard ${MULTISET_DIR}/$(basename $@)/$(patsubst .%,%,$(suffix $@))-*.xml)) -eq 0 ]; then \
	  d=$(firstword $(wildcard ${MULTISET_DIR}/$(basename $@)/*-$(patsubst .%,%,$(suffix $@)).xml)); \
	  grep 'linkGrp' $$d | tr " " "\n" | grep 'toDoc'|  cut -f2 -d\"   > $@; \
	  grep 'xtargets' $$d | cut -f2 -d\" | cut -f2 -d\;               >> $@; \
	else \
	  d=$(firstword $(wildcard ${MULTISET_DIR}/$(basename $@)/$(patsubst .%,%,$(suffix $@))-*.xml)); \
	  grep 'linkGrp' $$d | tr " " "\n" | grep 'fromDoc'|  cut -f2 -d\" > $@; \
	  grep 'xtargets' $$d | cut -f2 -d\" | cut -f1 -d\;               >> $@; \
	fi


cleanup:
	rm -f ${DOCLINKS}
	if [ -d multi-linksets-0.9 ]; then find multi-linksets-0.9 -delete; fi
