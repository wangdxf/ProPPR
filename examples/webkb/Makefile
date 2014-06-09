include ../Makefile.in

### PARAMETERS ###
ifeq ($(strip $(EPSILON)),)
EPSILON=1e-5
endif
ifeq ($(strip $(ALPHA)),)
ALPHA=0.01
endif
ifeq ($(strip $(PROVER)),)
PROVER=dpr:$(EPSILON):$(ALPHA)
endif
ifeq ($(strip $(THREADS)),)
THREADS=3
endif
ifeq ($(strip $(EPOCHS)),)
EPOCHS=10
endif

### DATASET
PROGRAM:=webkb0.crules:webkb.graph:webkb.cfacts
SUBSETS:=wisc wash texas cornell

### TARGETS

all: results.txt

clean:
	rm -f *results.txt *.params *.cooked *.log

results.txt: $(addsuffix .results.txt,$(addprefix pre.,$(SUBSETS)) $(addprefix post.,$(SUBSETS)))
	cat $^ > $@

%.results.txt: %.Tester.log
	tail -1 $< | awk '{print "$*",$$2,$$3,$$4,$$5,$$6,$$7,$$8,$$9}' > $@

pre.%.Tester.log: test_%.data
	java ${JOPTS} -cp ${CP} edu.cmu.ml.praprolog.Tester --programFiles ${PROGRAM} --prover ${PROVER} --test $< --threads ${THREADS} | tee pre.$*.Tester.log

post.%.Tester.log: test_%.data %.params
	java ${JOPTS} -cp ${CP} edu.cmu.ml.praprolog.Tester --programFiles ${PROGRAM} --prover ${PROVER} --test $< --threads ${THREADS} --params $*.params --tester rt | tee post.$*.Tester.log

%.params: train_no_%.data.cooked
	java ${JOPTS} -cp ${CP} edu.cmu.ml.praprolog.Trainer --prover ${PROVER} --train $< --params $@ --threads ${THREADS} --srw l2plocal --epochs ${EPOCHS}

train_no_%.data.cooked: train_no_%.data
	java ${JOPTS} -cp ${CP} edu.cmu.ml.praprolog.ExampleCooker --programFiles ${PROGRAM} --data $< --output $@ --prover ${PROVER} --threads ${THREADS}

.PRECIOUS: train_no_%.data.cooked %.params