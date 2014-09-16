
# This Makefile is for use by the exo Release Manager.
#
# requirements: Oort (http://code.google.com/p/owltools/wiki/OortIntro)
#
# To make a release, type 'make release' in this directory

OBO = http://purl.obolibrary.org/obo

initial-build: exo.obo
build: exo.obo minidiff
release: exo.obo diff

# all release files are derived from the editors version.
# we use the default Oort setup (hermit as reasoner)
exo.obo: exo-edit.owl
	ontology-release-runner $< --allow-overwrite --asserted --simple

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

