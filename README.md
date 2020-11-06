# A [CLASSLA](http://www.clarin.si/info/k-centre/) Fork of [Stanza](https://github.com/stanfordnlp/stanza) for Processing Slovene, Croatian, Serbian, Macedonian and Bulgarian

## Description

This pipeline allows for processing of standard Slovene, Croatian, Serbian and Bulgarian on the levels of

- tokenization and sentence splitting
- part-of-speech tagging
- lemmatization
- dependency parsing
- named entity recognition

It also allows for (alpha) processing of standard Macedonian on the levels of 

- tokenization and sentence splitting
- part-of-speech tagging
- lemmatization

Finally, it allows for processing of non-standard (Internet) Slovene, Croatian and Serbian on the same levels as standard language (all models are tailored to non-standard language except for dependency parsing where the standard module is used).

## Installation
### pip
We recommend that you install CLASSLA via pip, the Python package manager. To install, run:
```bash
pip install classla
```
This will also resolve all dependencies.

## Running CLASSLA

### Getting started

To run the CLASSLA pipeline for the first time on processing standard Slovene, follow these steps:

```
>>> import classla
>>> classla.download('sl')                            # download standard models for Slovene, use hr for Croatian, sr for Serbian, bg for Bulgarian
>>> nlp = classla.Pipeline('sl')                      # initialize the default Slovene pipeline, use hr for Croatian, sr for Serbian, bg for Bulgarian
>>> doc = nlp("France Prešeren je rojen v Vrbi.")     # run the pipeline
>>> print(doc.conll_file.conll_as_string())           # print the output in CoNLL-U format
# newpar id = 1
# sent_id = 1.1
# text = France Prešeren je rojen v Vrbi.
1	France	France	PROPN	Npmsn	Case=Nom|Gender=Masc|Number=Sing	4	nsubj	_	NER=B-per
2	Prešeren	Prešeren	PROPN	Npmsn	Case=Nom|Gender=Masc|Number=Sing	1	flat_name	_	NER=I-per
3	je	biti	AUX	Va-r3s-n	Mood=Ind|Number=Sing|Person=3|Polarity=Pos|Tense=Pres|VerbForm=Fin	4	cop	_	NER=O
4	rojen	rojen	ADJ	Appmsnn	Case=Nom|Definite=Ind|Degree=Pos|Gender=Masc|Number=Sing|VerbForm=Part	0	root	_	NER=O
5	v	v	ADP	Sl	Case=Loc	6	case	_	NER=O
6	Vrbi	Vrba	PROPN	Npfsl	Case=Loc|Gender=Fem|Number=Sing	4	obl	_	NER=B-loc|SpaceAfter=No
7	.	.	PUNCT	Z	_	4	punct	_	NER=O
```
You can find examples of standard language processing for [Croatian](#example-of-standard-croatian), [Serbian](#example-of-standard-serbian), [Macedonian](#example-of-standard-macedonian) and [Bulgarian](#example-of-standard-bulgarian) at the end of this document.

### Processing non-standard language

Processing non-standard Slovene differs to the above standard example just by an additional argument ```type="nonstandard"```:

```
>>> import classla
>>> classla.download('sl', type='nonstandard')        # download non-standard models for Slovene, use hr for Croatian and sr for Serbian
>>> nlp = classla.Pipeline('sl', type='nonstandard')  # initialize the default non-standard Slovene pipeline, use hr for Croatian and sr for Serbian
>>> doc = nlp("kva smo mi zurali zadnje leto v zagrebu...")     # run the pipeline
>>> print(doc.conll_file.conll_as_string()) 
1	kva	kaj	PRON	Pq-nsa	Case=Acc|Gender=Neut|Number=Sing|PronType=Int	4	obj	_	NER=O
2	smo	biti	AUX	Va-r1p-n	Mood=Ind|Number=Plur|Person=1|Polarity=Pos|Tense=Pres|VerbForm=Fin	4	aux	_	NER=O
3	mi	jaz	PRON	Pp1mpn	Case=Nom|Gender=Masc|Number=Plur|Person=1|PronType=Prs	nsubj	_	NER=O
4	zurali	žurati	VERB	Vmpp-pm	Aspect=Imp|Gender=Masc|Number=Plur|VerbForm=Part	root	_	NER=O
5	zadnje	zadnji	ADJ	Agpnsa	Case=Acc|Degree=Pos|Gender=Neut|Number=Sing	6	amod	_	NER=O
6	leto	leto	NOUN	Ncnsa	Case=Acc|Gender=Neut|Number=Sing	4	obl	NER=O
7	v	v	ADP	Sl	Case=Loc	8	case	_	NER=O
8	zagrebu	Zagreb	PROPN	Npmsl	Case=Loc|Gender=Masc|Number=Sing	4	obl	NER=B-LOC|SpaceAfter=No
9	...	.	PUNCT	Z	_	4	punct	_	NER=O

```

You can find examples of non-standard language processing for [Croatian](#example-of-non-standard-croatian) and [Serbian](#example-of-non-standard-serbian)  at the end of this document.

For additional usage examples you can also consult the ```pipeline_demo.py``` file.

## Processors

The CLASSLA pipeline is built from multiple units. These units are called processors. By default CLASSLA runs the ```tokenize```, ```ner```, ```pos```, ```lemma``` and ```depparse``` processors.

You can specify which processors `CLASSLA should run, via the ```processors``` attribute as in the following example, performing tokenization, named entity recognition, part-of-speech tagging and lemmatization.

```python
>>> nlp = classla.Pipeline('sl', processors='tokenize,ner,pos,lemma')
```

Another popular option might be to perform tokenization, part-of-speech tagging, lemmatization and dependency parsing.

```python
>>> nlp = classla.Pipeline('sl', processors='tokenize,pos,lemma,depparse')
```

### Tokenization and sentence splitting

The tokenization and sentence splitting processor ```tokenize``` is the first processor and is required for any further processing.

In case you already have tokenized text, you should separate tokens via spaces and pass the attribute ```tokenize_pretokenized=True```.

By default CLASSLA uses a rule-based tokenizer - [reldi-tokeniser](https://github.com/clarinsi/reldi-tokeniser).

<!--Most important attributes:
```
tokenize_pretokenized   - [boolean]     ignores tokenizer
```-->

### Part-of-speech tagging

The POS tagging processor ```pos``` will general output that contains morphosyntactic description following the [MULTEXT-East standard](http://nl.ijs.si/ME/V6/msd/html/msd.lang-specific.html) and universal part-of-speech tags and universal features following the [Universal Dependencies standard](https://universaldependencies.org). This processing requires the usage of the ```tokenize``` processor.

<!--Most important attributes:
```
pos_model_path          - [str]         alternative path to model file
pos_pretrain_path       - [str]         alternative path to pretrain file
```-->

### Lemmatization

The lemmatization processor ```lemma``` will produce lemmas (basic forms) for each token in the input. It requires the usage of both the ```tokenize``` and ```pos``` processors.

### Dependency parsing

The dependency parsing processor ```depparse``` performs syntactic dependency parsing of sentences following the [Universal Dependencies formalism](https://universaldependencies.org/introduction.html#:~:text=Universal%20Dependencies%20(UD)%20is%20a,from%20a%20language%20typology%20perspective.). It requires the ```tokenize``` and ```pos``` processors.

### Named entity recognition

The named entity recognition processor ```ner``` identifies named entities in text following the [IOB2](https://en.wikipedia.org/wiki/Inside–outside–beginning_(tagging)) format. It requires only the ```tokenize``` processor.

## Croatian examples

### Example of standard Croatian 

```
>>> import classla
>>> nlp = classla.Pipeline('hr') # run classla.download('hr') beforehand if necessary
>>> doc = nlp("Ante Starčević rođen je u Velikom Žitniku.")
>>> print(doc.conll_file.conll_as_string())
# newpar id = 1
# sent_id = 1.1
# text = Ante Starčević rođen je u Velikom Žitniku.
1	Ante	Ante	PROPN	Npmsn	Case=Nom|Gender=Masc|Number=Sing	3	nsubj_pass	_	NER=B-PER
2	Starčević	Starčević	PROPN	Npmsn	Case=Nom|Gender=Masc|Number=Sing	flat	_	NER=I-PER
3	rođen	roditi	ADJ	Appmsnn	Case=Nom|Definite=Ind|Degree=Pos|Gender=Masc|Number=Sing|VerbForm=Part|Voice=Pass	0	root	_	NER=O
4	je	biti	AUX	Var3s	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	aux_pass	_	NER=O
5	u	u	ADP	Sl	Case=Loc	7	case	_	NER=O
6	Velikom	velik	ADJ	Agpmsly	Case=Loc|Definite=Def|Degree=Pos|Gender=Masc|Number=Singamod	_	NER=B-LOC
7	Žitniku	Žitnik	PROPN	Npmsl	Case=Loc|Gender=Masc|Number=Sing	3	obl	NER=I-LOC|SpaceAfter=No
8	.	.	PUNCT	Z	_	3	punct	_	NER=O

```
### Example of non-standard Croatian

```
>>> import classla
>>> nlp = classla.Pipeline('hr', type='nonstandard') # run classla.download('hr', type='nonstandard') beforehand if necessary
>>> doc = nlp("kaj sam ja tulumaril jucer u ljubljani...")
>>> print(doc.conll_file.conll_as_string())
1	kaj	što	PRON	Pi3n-a	Case=Acc|Gender=Neut|PronType=Int,Rel	4	obj	NER=O
2	sam	biti	AUX	Var1s	Mood=Ind|Number=Sing|Person=1|Tense=Pres|VerbForm=Fin	aux	_	NER=O
3	ja	ja	PRON	Pp1-sn	Case=Nom|Number=Sing|Person=1|PronType=Prs	4	nsubj	_	NER=O
4	tulumaril	tulumariti	VERB	Vmp-sm	Gender=Masc|Number=Sing|Tense=Past|VerbForm=Part|Voice=Act	0	root	_	NER=O
5	jucer	jučer	ADV	Rgp	Degree=Pos	4	advmod	_	NER=O
6	u	u	ADP	Sl	Case=Loc	7	case	_	NER=O
7	ljubljani	Ljubljana	PROPN	Npfsl	Case=Loc|Gender=Fem|Number=Sing	4	obl	_	NER=B-LOC|SpaceAfter=No
8	...	.	PUNCT	Z	_	4	punct	_	NER=O

```

## Serbian examples

### Example of standard Serbian

```
>>> import classla
>>> nlp = classla.Pipeline('sr') # run classla.download('sr') beforehand if necessary
>>> doc = nlp("Slobodan Jovanović rođen je u Novom Sadu.")
>>> print(doc.conll_file.conll_as_string())
# newpar id = 1
# sent_id = 1.1
# text = Slobodan Jovanović rođen je u Novom Sadu.
1	Slobodan	Slobodan	PROPN	Npmsn	Case=Nom|Gender=Masc|Number=Sing	nsubj	_	NER=B-PER
2	Jovanović	Jovanović	PROPN	Npmsn	Case=Nom|Gender=Masc|Number=Sing	flat	_	NER=I-PER
3	rođen	roditi	ADJ	Appmsnn	Case=Nom|Definite=Ind|Degree=Pos|Gender=Masc|Number=Sing|VerbForm=Part|Voice=Pass	0	root	_	NER=O
4	je	biti	AUX	Var3s	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	aux	_	NER=O
5	u	u	ADP	Sl	Case=Loc	6	case	_	NER=O
6	Novom	nov	ADJ	Agpmsly	Case=Loc|Definite=Def|Degree=Pos|Gender=Masc|Number=Singobl	_	NER=B-LOC
7	Sadu	Sad	PROPN	Npmsl	Case=Loc|Gender=Masc|Number=Sing	6	flat	NER=I-LOC|SpaceAfter=No
8	.	.	PUNCT	Z	_	3	punct	_	NER=O

```

### Example of non-standard Serbian

```
>>> import classla
>>> nlp = classla.Pipeline('sr', type='nonstandard') # run classla.download('sr', type='nonstandard') beforehand if necessary
>>> doc = nlp("ne mogu da verujem kakvo je zezanje bilo prosle godine u zagrebu...")
>>> print(doc.conll_file.conll_as_string())
# newpar id = 1
# sent_id = 1.1
# text = ne mogu da verujem kakvo je zezanje bilo prosle godine u zagrebu...
1	ne	ne	PART	Qz	Polarity=Neg	2	advmod	_	NER=O
2	mogu	moći	VERB	Vmr1s	Mood=Ind|Number=Sing|Person=1|Tense=Pres|VerbForm=Fin	root	_	NER=O
3	da	da	SCONJ	Cs	_	4	mark	_	NER=O
4	verujem	verovati	VERB	Vmr1s	Mood=Ind|Number=Sing|Person=1|Tense=Pres|VerbForm=Fin	2	xcomp	_	NER=O
5	kakvo	kakav	DET	Pi-nsn	Case=Nom|Gender=Neut|Number=Sing|PronType=Int,Rel	ccomp	_	NER=O
6	je	biti	AUX	Var3s	Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin	aux	_	NER=O
7	zezanje	zezanje	NOUN	Ncnsn	Case=Nom|Gender=Neut|Number=Sing	5	nsubj	NER=O
8	bilo	biti	AUX	Vap-sn	Gender=Neut|Number=Sing|Tense=Past|VerbForm=Part|Voice=Act	5	cop	_	NER=O
9	prosle	prošli	ADJ	Agpfsgy	Case=Gen|Definite=Def|Degree=Pos|Gender=Fem|Number=Sing	10	amod	_	NER=O
10	godine	godina	NOUN	Ncfsg	Case=Gen|Gender=Fem|Number=Sing	8	obl	_	NER=O
11	u	u	ADP	Sl	Case=Loc	12	case	_	NER=O
12	zagrebu	Zagreb	PROPN	Npmsl	Case=Loc|Gender=Masc|Number=Sing	8	obl	NER=B-LOC|SpaceAfter=No
13	...	.	PUNCT	Z	_	2	punct	_	NER=O

```

## Bulgarian examples

### Example of standard Bulgarian

```
>>> import classla
>>> nlp = classla.Pipeline('bg') # run classla.download('bg') beforehand if necessary
>>> doc = nlp("Алеко Константинов е роден в Свищов.")
>>> print(doc.conll_file.conll_as_string())
# newpar id = 1
# sent_id = 1.1
# text = Алеко Константинов е роден в Свищов.
1	Алеко	алеко	PROPN	Npmsi	Definite=Ind|Gender=Masc|Number=Sing	4	nsubj:pass	_	NER=B-PER
2	Константинов	константинов	PROPN	Hmsi	Definite=Ind|Gender=Masc|Number=Sing	flat	_	NER=I-PER
3	е	съм	AUX	Vxitf-r3s	Aspect=Imp|Mood=Ind|Number=Sing|Person=3|Tense=Pres|VerbForm=Fin|Voice=Act	4	aux:pass	_	NER=O
4	роден	родя-(се)	VERB	Vpptcv--smi	Aspect=Perf|Definite=Ind|Gender=Masc|Number=Sing|VerbForm=Part|Voice=Pass	0	root	_	NER=O
5	в	в	ADP	R	_	6	case	_	NER=O
6	Свищов	свищов	PROPN	Npmsi	Definite=Ind|Gender=Masc|Number=Sing	4	iobj	NER=B-LOC|SpaceAfter=No
7	.	.	PUNCT	punct	_	4	punct	_	NER=O

```

## Macedonian examples

### Example of standard Macedonian

```
>>> import classla
>>> nlp = classla.Pipeline('mk') # run classla.download('mk') beforehand if necessary
>>> doc = nlp('Крсте Петков Мисирков е роден во Постол.')
>>> print(doc.conll_file.conll_as_string())
# newpar id = 1
# sent_id = 1.1
# text = Крсте Петков Мисирков е роден во Постол.
1	Крсте	крсте	ADJ	Afpms-n	Definite=Ind|Gender=Masc|Number=Sing	_	_	_	_
2	Петков	петков	NOUN	Ncmsnn	Case=Nom|Definite=Ind|Gender=Masc|Number=Sing	_	_	_	_
3	Мисирков	мисирков	NOUN	Ncmsnn	Case=Nom|Definite=Ind|Gender=Masc|Number=Sing	_	_	_	_
4	е	сум	AUX	Vapip3s-n	Aspect=Prog|Mood=Ind|Number=Sing|Person=3|Polarity=Pos|Tense=Pres	_	_
5	роден	роден	ADJ	Ap-ms-n	Definite=Ind|Gender=Masc|Number=Sing|VerbForm=Part	_	_	_	_
6	во	во	ADP	Sps	AdpType=Prep	_	_	_	_
7	Постол	постол	NOUN	Ncmsnn	Case=Nom|Definite=Ind|Gender=Masc|Number=Sing	_	_	_	SpaceAfter=No
8	.	.	PUNCT	Z	_	_	_	_	_

```
