TEMPLATE = template.j2
SOFFICE = /Applications/LibreOffice.app/Contents/MacOS/soffice
YAMLS = $(wildcard *.yaml)

PDFS=$(YAMLS:.yaml=.pdf)
ODT=$(YAMLS:.yaml=.odt)

all: $(PDFS) $(ODT)

%.md: %.yaml 
	jinja2 --format yaml $(TEMPLATE) $< > $@

%.odt: %.md
	pandoc --reference-odt odt/template.odt -o $@ $<

%.pdf: %.odt
	$(SOFFICE) -env:UserInstallation="file:///tmp/LibO_Conversion" --headless --invisible --convert-to pdf $<


clean: rm -f *.pdf *.odt
