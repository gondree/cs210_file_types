# Makefiles that import this makefile
# must define
# 	CURRENT = the master latex file to be built
# and optionally define 
# 	LOCAL_TEXINPUTS = the location of local macros, style files, etc.

ifdef LOCAL_TEXINPUTS
	ifdef TEXINPUTS
		#do not forget trailing ":".  Without it, system inputs will
		#not be searched.
		export TEXINPUTS := $(TEXINPUTS):$(LOCAL_TEXINPUTS):
	else
		export TEXINPUTS := $(LOCAL_TEXINPUTS):
	endif
endif
ifdef LOCAL_BIBINPUTS
	ifdef BIBINPUTS
		export BIBINPUTS := $(BIBINPUTS):$(LOCAL_BIBINPUTS):
	else
		export BIBINPUTS := $(LOCAL_BIBINPUTS):
	endif
endif
TEX_INPUTS = $(shell find . -name \*.tex)
BIB_FILES = $(shell find ./bib -name \*.bib)
XFIG_FIGURES = $(shell find ./figures -name \*.fig)
XFIG_BAK = $(wildcard figures/*.bak)
XFIG_PDFTEX_T = $(XFIG_FIGURES:fig=pdftex_t)
XFIG_PDF = $(XFIG_FIGURES:fig=pdf)

export latex_count=3

#Without -p, graphics are not output, only text.
%.pdftex_t: %.fig
	fig2dev -Lpdftex -p0 $< > $(<:fig=pdf)
	fig2dev -Lpdftex_t -p$(<:fig=pdf) $< > $(<:fig=pdftex_t)

$(CURRENT).pdf: $(TEX_INPUTS) $(XFIG_PDFTEX_T) $(CURRENT).bbl
#$(CURRENT).pdf: $(TEX_INPUTS) $(XFIG_PDFTEX_T) $(BIB_BBL)
	pdflatex --interaction errorstopmode $(CURRENT)
	while egrep -s 'Rerun (LaTeX|to get cross-references right)' $(CURRENT).log && [[ $$latex_count -gt 0 ]] ;\
	    do \
		echo "Rerunning pdflatex...." ;\
		pdflatex --interaction errorstopmode $(CURRENT) ;\
		latex_count=`expr $$latex_count - 1` ;\
	    done

$(CURRENT).bbl: $(BIB_FILES)
	pdflatex $(CURRENT) 
	-bibtex $(CURRENT)

.PHONY: clean
clean:
	rm -f $(CURRENT).bbl $(CURRENT).blg $(CURRENT).log *.aux *~
	rm -f $(XFIG_PDFTEX_T) $(XFIG_PS) $(XFIG_PDF) $(XFIG_BAK)

.PHONY: cleaner
cleaner: clean
	rm -f $(CURRENT).pdf
