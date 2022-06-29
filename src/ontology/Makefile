
# This Makefile is for use by the exo Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

OBO = http://purl.obolibrary.org/obo
URIBASE=                    http://purl.obolibrary.org/obo
ONT=                        exo
ONTBASE=                    $(URIBASE)/$(ONT)
SRC =                       exo-edit.obo
CATALOG=                    catalog-v001.xml
ROBOT=                      robot --catalog $(CATALOG)
TODAY ?=                    $(shell date +%Y-%m-%d)
OBODATE ?=                  $(shell date +'%d:%m:%Y %H:%M')
VERSION=                    $(TODAY)
ANNOTATE_ONTOLOGY_VERSION = annotate -V $(ONTBASE)/releases/$(VERSION)/$@ --annotation owl:versionInfo $(VERSION)


initial-build: exo.obo
build: exo.obo minidiff

prepare_release: exo.obo exo.owl
	cp $^ ../..

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
#exo.obo: exo-edit.owl
#	ontology-release-runner $< --allow-overwrite --asserted --simple

# ----------------------------------------
# Imports
# ----------------------------------------
all_imports: imports/go_import.owl imports/pato_import.owl

imports/%_import.owl: exo-edit.owl
	owltools $< $(OBO)/$*.owl --add-imports-from-supports --extract-module -s $(OBO)/$*.owl -c --extract-mingraph  --set-ontology-id $(OBO)/go/extensions/$@ -o $@

# ----------------------------------------
# Diffs
# ----------------------------------------

# 
diff: exo.obo
	cd diffs && touch exo-diff && rm exo-*diff* && make

# Minidiff: intra-release differences
minidiff: exo.obo
	cd diffs  && touch exo-diff && rm exo-*diff* && make TGTS='html txt' SRC=../exo.obo && mail -s exo-diffs RELEASE-MANAGER@FOO.BAR < exo-combined-diff.txt

exo.obo: exo.owl
	$(ROBOT) convert --input $< --check false -f obo -o $@.tmp.obo && grep -v ^owl-axioms $@.tmp.obo > $@ && rm $@.tmp.obo

exo.owl: $(SRC)
	$(ROBOT) merge --input $< \
		reason --reasoner ELK --equivalent-classes-allowed asserted-only --exclude-tautologies structural \
		relax \
		reduce -r ELK \
		annotate --ontology-iri $(URIBASE)/$@ $(ANNOTATE_ONTOLOGY_VERSION) --output $@.tmp.owl && mv $@.tmp.owl $@

