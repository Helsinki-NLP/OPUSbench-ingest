# -*-makefile-*-


include ../Makefile.def
include ../../Makefile.def
include ../../Makefile.submit


all: ${LANGUAGES}
	if [ -d OpenSubtitles ]; then find OpenSubtitles -delete; fi

RAWSTORAGE = https://object.pouta.csc.fi/OPUS-OpenSubtitles-devtest/devtest-raw/encrypted

${LANGUAGES}:
	wget -q ${RAWSTORAGE}/$@.zip
	-zgrep fromDoc ../xml/$@-*.xml.gz | tr " " "\n" | grep fromDoc | cut -f2 -d\" > $@.files
	-zgrep fromDoc ../xml/*-$@.xml.gz | tr " " "\n" | grep toDoc | cut -f2 -d\"  >> $@.files
	sort -u $@.files | sed 's/^/OpenSubtitles\/raw\//' | sed 's/\.gz//' > $@.sorted
	if [ -s $@.sorted ]; then \
	  cat $@.sorted | xargs unzip -P OpenSubtitles2024-devtest $@.zip; \
	  mv OpenSubtitles/raw/$@ $@; \
	fi
	rm -f $@.sorted $@.files $@.zip
