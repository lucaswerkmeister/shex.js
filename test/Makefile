# debugging: MOCHA='mocha --debug-brk'

JISON?=../node_modules/.bin/jison
MOCHA?=../node_modules/.bin/mocha
TESTDIR?=../../shexTest/ # needs trailing '/'
GENJSON?=$(join $(TESTDIR), bin/genJSON.js)
GENCorpus?=$(join $(TESTDIR), bin/genCorpus.js)
REPORTER?=dot
BRANCH?=$(shell git rev-parse --abbrev-ref HEAD) # e.g. master or ShEx1
HTTPTEST?="http://raw.githubusercontent.com/shexSpec/shex.js/$(BRANCH)/test/"

ValidationManifest=$(join $(TESTDIR), validation/manifest.jsonld) # isn't there an easier way to construct filenames?
ValidationCorpus=$(join $(TESTDIR), schemas/corpus.json) # isn't there an easier way to construct filenames?
SHEXSCHEMAS = $(wildcard $(join $(TESTDIR), schemas/*.shex))
JSONSCHEMAS = $(SHEXSCHEMAS:shex=json)

../lib/ShExJison.js: ../lib/ShExJison.jison
	$(JISON) $^ -o $@ -p lalr

../lib/ShapeMapJison.js: ../lib/ShapeMapJison.jison
	$(JISON) $^ -o $@ -p lalr

Parser-Writer-test: ../lib/ShExJison.js ../lib/ShapeMapJison.js Parser-Writer-test.js
	$(MOCHA) -C -R $(REPORTER) Parser-Writer-test.js

Partition-test: ../lib/ShExJison.js ../lib/ShapeMapJison.js Partition-test.js
	$(MOCHA) -C -R $(REPORTER) Partition-test.js

Compilation-test: ../lib/ShExJison.js ../lib/ShapeMapJison.js ../lib/ShExUtil.js Compilation-test.js
	$(MOCHA) -C -R $(REPORTER) Compilation-test.js

$(ValidationManifest): $(join $(TESTDIR), validation/manifest.ttl) $(GENJSON)
	$(GENJSON) $< -o $@ -e # suppress output if there are errors

$(ValidationCorpus): $(GENCorpus) $(JSONSCHEMAS)
	$(GENCorpus) $< -d $(join $(TESTDIR), schemas/) -o $@ -e # suppress output if there are errors

Validation-test: ../lib/ShExJison.js ../lib/ShapeMapJison.js ../lib/ShExValidator.js Validation-test.js $(ValidationManifest)
	$(MOCHA) -C -R $(REPORTER) Validation-test.js

Map-test: ../lib/ShExJison.js ../lib/ShapeMapJison.js Map-test.js
	$(MOCHA) -C -R $(REPORTER) Map-test.js

cli-test: ../lib/ShExJison.js ../lib/ShapeMapJison.js cli-test.js
	$(MOCHA) -C -R $(REPORTER) cli-test.js

test: Parser-Writer-test Partition-test Compilation-test Validation-test Map-test cli-test

