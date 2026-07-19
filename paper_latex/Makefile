PAPER = omni_paper

all: $(PAPER).pdf

$(PAPER).pdf: $(PAPER).tex omni_references.bib
	pdflatex $(PAPER).tex
	bibtex $(PAPER)
	pdflatex $(PAPER).tex
	pdflatex $(PAPER).tex

clean:
	rm -f *.aux *.bbl *.blg *.log *.out *.toc *.fls *.fdb_latexmk

distclean: clean
	rm -f $(PAPER).pdf

.PHONY: all clean distclean
