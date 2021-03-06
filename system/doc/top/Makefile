#
# %CopyrightBegin%
#
# Copyright Ericsson AB 1999-2018. All Rights Reserved.
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
#
# %CopyrightEnd%
#
#
include $(ERL_TOP)/make/target.mk
include $(ERL_TOP)/make/$(TARGET)/otp.mk

# ----------------------------------------------------
# Application version
# ----------------------------------------------------
include $(ERL_TOP)/erts/vsn.mk

APPLICATION=otp-system-documentation

# ----------------------------------------------------
# Release directory specification
# ----------------------------------------------------
RELSYSDIR = "$(RELEASE_PATH)/doc"

GIF_FILES = 

INFO_FILES = ../../../README.md ../../COPYRIGHT PR.template

TOPDOCDIR=.

include ../installation_guide/xmlfiles.mk
include ../system_principles/xmlfiles.mk
include ../embedded/xmlfiles.mk
include ../getting_started/xmlfiles.mk
include ../reference_manual/xmlfiles.mk
include ../programming_examples/xmlfiles.mk
include ../efficiency_guide/xmlfiles.mk
include ../tutorial/xmlfiles.mk
include ../design_principles/xmlfiles.mk
include ../oam/xmlfiles.mk

BOOK_FILES = book.xml

XML_FILES = \
	$(BOOK_FILES)

XML_GUIDE_FILES = \
	$(INST_GUIDE_CHAPTER_FILES:%=installation_guide/%) \
	$(INST_GUIDE_CHAPTER_GEN_FILES:%=installation_guide/%) \
	$(SYSTEM_PRINCIPLES_CHAPTER_FILES:%=system_principles/%) \
	$(SYSTEM_PRINCIPLES_CHAPTER_GEN_FILES:%=system_principles/%) \
	$(EMBEDDED_CHAPTER_FILES:%=embedded/%) \
	$(EMBEDDED_CHAPTER_GEN_FILES:%=embedded/%) \
	$(GETTING_STARTED_CHAPTER_FILES:%=getting_started/%) \
	$(GETTING_STARTED_CHAPTER_GEN_FILES:%=getting_started/%) \
	$(REF_MAN_CHAPTER_FILES:%=reference_manual/%) \
	$(REF_MAN_CHAPTER_GEN_FILES:%=reference_manual/%) \
	$(PROG_EX_CHAPTER_FILES:%=programming_examples/%) \
	$(PROG_EX_CHAPTER_GEN_FILES:%=programming_examples/%) \
	$(EFF_GUIDE_CHAPTER_FILES:%=efficiency_guide/%) \
	$(EFF_GUIDE_CHAPTER_GEN_FILES:%=efficiency_guide/%) \
	$(TUTORIAL_CHAPTER_FILES:%=tutorial/%) \
	$(TUTORIAL_CHAPTER_GEN_FILES:%=tutorial/%) \
	$(DESIGN_PRINCIPLES_CHAPTER_FILES:%=design_principles/%) \
	$(DESIGN_PRINCIPLES_CHAPTER_GEN_FILES:%=design_principles/%) \
	$(OAM_CHAPTER_FILES:%=oam/%) \
	$(OAM_CHAPTER_GEN_FILES:%=oam/%)

XML_GEN_FILES = \
	$(XML_GUIDE_FILES:%=$(XMLDIR)/%) \
	$(XMLDIR)/installation_guide/part.xml \
	$(XMLDIR)/system_principles/part.xml \
	$(XMLDIR)/embedded/part.xml \
	$(XMLDIR)/getting_started/part.xml \
	$(XMLDIR)/reference_manual/part.xml \
	$(XMLDIR)/programming_examples/part.xml \
	$(XMLDIR)/efficiency_guide/part.xml \
	$(XMLDIR)/tutorial/part.xml \
	$(XMLDIR)/design_principles/part.xml \
	$(XMLDIR)/oam/part.xml


XMLLINT_SRCDIRS= $(XMLDIR)/installation_guide:$(XMLDIR)/system_principles:$(XMLDIR)/embedded:$(XMLDIR)/getting_started:$(XMLDIR)/reference_manual:$(XMLDIR)/programming_examples:$(XMLDIR)/efficiency_guide:$(XMLDIR)/tutorial:$(XMLDIR)/design_principles:$(XMLDIR)/oam
HTMLDIR= ../html
PDFREFDIR= pdf

TOP_PDF_FILE = $(PDFDIR)/$(APPLICATION)-$(VSN).pdf
TOPDOC=true

#--------------------------------------------------------------------------
# We generate the index page from the installed system. This make
# it important that this is done last. The file index.html.src
# is used as a template for the resulting page.

EBIN = ebin

INDEX_SCRIPT = $(EBIN)/erl_html_tools.$(EMULATOR)
INDEX_SRC = src/erl_html_tools.erl
INDEX_FILES = \
	$(HTMLDIR)/index.html \
	$(HTMLDIR)/applications.html 

JAVASCRIPT = $(HTMLDIR)/js/erlresolvelinks.js
JAVASCRIPT_BUILD_SCRIPT = $(EBIN)/erlresolvelinks.$(EMULATOR)
JAVASCRIPT_BUILD_SCRIPT_SRC = src/erlresolvelinks.erl

MAN_INDEX_SCRIPT = $(EBIN)/otp_man_index.$(EMULATOR)
MAN_INDEX_SRC = src/otp_man_index.erl
MAN_INDEX = $(HTMLDIR)/man_index.html

GLOSSARY  = $(HTMLDIR)/glossary.html
GLOSSARY_SRC = $(ERL_TOP)/system/internal_tools/doctools/src/glossary.erl
GLOSSARY_SCRIPT = $(EBIN)/glossary.$(EMULATOR)

TEMPLATES = \
	templates/index.html.src \
	templates/applications.html.src

#--------------------------------------------------------------------------

$(INDEX_SCRIPT): $(INDEX_SRC)
	$(ERLC) -o$(EBIN) +warn_unused_vars $<

# We don't list toc_*.html as targets because we don't know
$(HTMLDIR)/index.html + $(HTMLDIR)/applications.html: $(INDEX_SCRIPT) $(TEMPLATES)
	echo "Generating index $@"
# Check if we are building the index from source or an installed release
	if test "$$RELEASE_ROOT" = "" ; then \
		$(ERL) -noshell -pa $(EBIN) -s erl_html_tools top_index src $(ERL_TOP) \
		$(HTMLDIR) `cat "$(ERL_TOP)/OTP_VERSION"` -s erlang halt ;\
	else \
		$(ERL) -noshell -pa $(EBIN) -s erl_html_tools top_index rel $(RELEASE_ROOT) \
		$(HTMLDIR) `cat "$(RELEASE_ROOT)/releases/$(SYSTEM_VSN)/OTP_VERSION"` \
		-s erlang halt ;\
	fi


#--------------------------------------------------------------------------

$(JAVASCRIPT_BUILD_SCRIPT): $(JAVASCRIPT_BUILD_SCRIPT_SRC)
	$(ERLC) -o$(EBIN) +warn_unused_vars $<

$(JAVASCRIPT): $(JAVASCRIPT_BUILD_SCRIPT)
	erl -noshell -pa $(EBIN) -s erlresolvelinks make -s erlang halt
	$(INSTALL_DIR) $(HTMLDIR)/js
	$(INSTALL_DATA) erlresolvelinks.js $(JAVASCRIPT) 

#--------------------------------------------------------------------------

$(MAN_INDEX_SCRIPT): $(MAN_INDEX_SRC)
	$(ERLC) -o$(EBIN) +warn_unused_vars $<

$(MAN_INDEX): $(MAN_INDEX_SCRIPT)
# Check if we are building the index from source or an installed release
	if test "$$RELEASE_ROOT" = "" ; then \
		$(ERL) -noshell -pa $(EBIN) -s otp_man_index gen src $(ERL_TOP) $@  \
			-s erlang halt ;\
	else \
		$(ERL) -noshell -pa $(EBIN) -s otp_man_index gen rel $(RELEASE_ROOT) $@  \
			-s erlang halt ;\
	fi


#--------------------------------------------------------------------------

$(HTMLDIR)/highlights.html:  highlights.xml
	date=`date +"%B %e, %Y"`; \
	$(XSLTPROC) --output $(@) \
		--stringparam docgen "$(DOCGEN)" \
		--stringparam topdocdir "$(TOPDOCDIR)" \
		--stringparam pdfdir "$(PDFREFDIR)"  \
		--stringparam gendate "$$date" \
		--stringparam appname "$(APPLICATION)" \
		--stringparam appver "$(VSN)" \
		--stringparam stylesheet "$(CSS_FILE)" \
		--stringparam winprefix "$(WINPREFIX)" \
		--stringparam logo "$(HTMLLOGO_FILE)" \
		-path  $(DOCGEN)/priv/dtd \
		-path  $(DOCGEN)/priv/dtd_html_entities \
		$(DOCGEN)/priv/xsl/db_html.xsl $<


$(HTMLDIR)/incompatible.html:  incompatible.xml
	date=`date +"%B %e, %Y"`; \
	$(XSLTPROC) --output $(@) --stringparam docgen "$(DOCGEN)" \
		--stringparam topdocdir "$(TOPDOCDIR)" \
		--stringparam pdfdir "$(PDFREFDIR)"  \
		--stringparam gendate "$$date" \
		--stringparam appname "$(APPLICATION)" \
		--stringparam appver "$(VSN)" \
		--stringparam stylesheet "$(CSS_FILE)" \
		--stringparam winprefix "$(WINPREFIX)" \
		--stringparam logo "$(HTMLLOGO_FILE)" \
		-path  $(DOCGEN)/priv/dtd \
		-path  $(DOCGEN)/priv/dtd_html_entities \
		$(DOCGEN)/priv/xsl/db_html.xsl $<

#--------------------------------------------------------------------------

$(GLOSSARY_SCRIPT): $(GLOSSARY_SRC)
	$(ERLC) -o$(EBIN) $(GLOSSARY_SRC)

$(GLOSSARY): $(GLOSSARY_SCRIPT)
	$(ERL) -noshell -pa $(EBIN) \
		-s glossary make ../definitions/term.defs -s erlang halt > \
		$(GLOSSARY)

#--------------------------------------------------------------------------

PR.template: PR.template.src $(ERL_TOP)/make/$(TARGET)/otp.mk
	sed -e 's;%VSN%;$(VSN);' \
	    -e 's;%SYSTEM_VSN%;$(SYSTEM_VSN);' \
	    $< > $@

# ----------------------------------------------------
# FLAGS 
# ----------------------------------------------------
XML_FLAGS += 
DVIPS_FLAGS += 

# ----------------------------------------------------
# Targets
# ----------------------------------------------------


docs: pdf html $(INFO_FILES)

local_docs: PDFREFDIR=../pdf

$(TOP_PDF_FILE): $(XML_FILES)

pdf: $(TOP_PDF_FILE)

html:   $(INDEX_FILES) \
	$(MAN_INDEX) $(JAVASCRIPT)

debug opt: 

clean:
	rm -rf 	../html/js
	rm -f PR.template 
	rm -f  $(INDEX_FILES) $(MAN_INDEX)
	rm -f  $(TOP_PDF_FILE) $(TOP_PDF_FILE:%.pdf=%.fo)
	rm -f $(INDEX_SCRIPT) $(GLOSSARY_SCRIPT) \
		$(JAVASCRIPT_BUILD_SCRIPT) 
	rm -f erl_crash.dump errs core *~
# ----------------------------------------------------
# Release Target
# ---------------------------------------------------- 
include $(ERL_TOP)/make/otp_release_targets.mk

release_docs_spec: docs
	$(INSTALL_DIR)  "$(RELEASE_PATH)"
	$(INSTALL_DATA) $(INFO_FILES) "$(RELEASE_PATH)"
	$(INSTALL_DIR)  $(RELSYSDIR)
	$(INSTALL_DIR)  $(RELSYSDIR)/pdf
	$(INSTALL_DATA)  \
		$(TOP_PDF_FILE) $(RELSYSDIR)/pdf
	$(INSTALL_DIR)  $(RELSYSDIR)/js
	$(INSTALL_DATA)  \
		$(JAVASCRIPT) $(RELSYSDIR)/js
	$(INSTALL_DATA) $(INDEX_FILES) $(MAN_INDEX) $(RELSYSDIR)
	$(INSTALL_DIR)  $(RELSYSDIR)/docbuild
	$(INSTALL_DATA) $(INDEX_SCRIPT) $(MAN_INDEX_SCRIPT) $(JAVASCRIPT_BUILD_SCRIPT) \
		$(INDEX_SRC) $(MAN_INDEX_SRC) $(JAVASCRIPT_BUILD_SCRIPT_SRC) \
		$(TEMPLATES) $(RELSYSDIR)/docbuild


release_spec:
