# cor-asv-ann-models

Pretrained models for the [ANN-based post-correction module](https://github.com/ASVLeipzig/cor-asv-ann).

 * [Models](#models)
    * [OCR post-correction](#ocr-post-correction)
    * [Other](#other)
 * [Evaluation](#evaluation)
    * [Datasets](#datasets)
    * [OCR models](#ocr-models)
    * [Rejection](#rejection)
    * [Domain](#domain)
    * [Character Error Rate (CER)](#character-error-rate-cer)
    * [Results](#results)
    * [Discussion](#discussion)

## Models

### OCR post-correction

All models use `cor-asv-ann-train --width 512 --depth 2` and were initialised with weights from a [language model](https://github.com/OCR-D/ocrd_keraslm) via `--init-model`, then pretrained on 200k lines of clean text (input=output) from [DTA](https://www.deutsches-textarchiv.de), and then retrained via `--load-model` on [GT4HistOCR](https://zenodo.org/record/1344132/export/hx) and [OCR-D GT](http://www.ocr-d.de/gt), processed by various OCR models (input=OCR with confidence, output=GT). The latter is allowed to change _all_ weights (not just fine-tuning) and does _not_ reset the encoder layer weights (`--reset-encoder`).

- [dta19.Fraktur4](s2s.dta19.Fraktur4.d2.w0512.adam.attention.stateless.variational-dropout.char.pretrained+retrained-conf.h5): on 19th century Fraktur texts (GT4HistOCR/corpus/dta19) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `script/Fraktur`
- [pre19.Fraktur4](s2s.pre19.Fraktur4.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `script/Fraktur`
- [pre19.deu-frak3](s2s.pre19.deu-frak3.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 3 model](https://github.com/tesseract-ocr/tessdata) `deu-frak`
- [pre19.Latin4](s2s.pre19.Latin4.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `script/Latin`
- [pre19.deu4](s2s.pre19.deu4.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `deu`
- [pre19.incunabula](s2s.pre19.incunabula.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Ocropus 1](https://github.com/ocropus/ocropy) model `incunabula.pyrnn` included in GT4HistOCR
- [pre19.latinhist](s2s.pre19.latinhist.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Ocropus 1](https://github.com/ocropus/ocropy) model `latinhist.pyrnn` included in GT4HistOCR

### Other

- [gt4histocr.s-ſ](s2s.gt4histocr.s-ſ.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.h5): on GT4HistOCR ground truth degraded by replacing `ſ` into `s` in the input (encouraging the network to learn its reconstruction)

## Evaluation

Above models were run against OCR results from various models on combinations of the above datasets.

### Datasets

- `GT4HistOCR/dta19` signifies the `dta19` subcorpus of GT4HistOCR
- `GT4HistOCR/!dta19` signifies the `Kallimachos` and `Incunabula` subcorpus (i.e. everything but the `dta19` subset) of GT4HistOCR
- `OCR-D<19` signifies the 15-18th century bags of the [OCR-D ground truth repository](https://ocr-d-repo.scc.kit.edu/api/v1/metastore/bagit/search) (i.e. `ocrd_data_structur_text_*_1[4567]*`)

In each case, evaluation was done on a (2-10%) validation subset not seen during training (totalling _#lines_ lines).

### OCR models

The names are the same as for post-correction training (see [above](#ocr-post-correction)):
- `Fraktur4` is the Tesseract 4 model `script/Fraktur`
- `Latin4` is the Tesseract 4 model `script/Latin`
- `deu4` is the Tesseract 4 model `deu`
- `frk4` is the Tesseract 4 model `frk`
- `deu+frk4` is Tesseract `deu+frk`
- `deu-frak3` is the Tesseract 3 model `deu-frak`
- `ocrofraktur` is the Ocropus [Fraktur model](https://github.com/zuphilip/ocropy-models/raw/master/fraktur.pyrnn.gz)
- `ocrofraktur-jze` is the Ocropus [Fraktur model by jze](https://github.com/jze/ocropus-model_fraktur)
- `incunabula` is the Ocropus model by that name included in GT4HistOCR
- `latinhist` is the Ocropus model by that name included in GT4HistOCR
- `GT4HistOCR` is the [Tesseract model trained on GT4HistOCR](https://github.com/tesseract-ocr/tesstrain/wiki/GT4HistOCR) (for which unfortunately no post-correction model could be trained during the project runtime, because it came too late).

### Rejection

Rejection threshold (RT) has been set to various levels, but while 0.0 _will_ disable rejection completely (i.e. the input hypothesis, if at all identifiable, will keep its predicted score), 1.0 will _not_ disable correction completely (because the input hypothesis might not be found if alignment is too bad). 

We usually set it to 0.9 for out-domain and 0.2 for in-domain tasks, but vary where other settings yield better results.

### Domain

Inductive-deductive post-correction expects to see data during inference that is similar to _both_ the dataset _and_ the OCR model during training. When this (narrow) circumstance is met, we have the in-domain case, the other is referred to as out-domain.

### Character Error Rate (CER)

Quality is measured by aligning output and GT lines, calculating the (unweighted) Levenshtein distance along the best path, and dividing it by the length of that path (not of the GT sequence). Rates are aggregated across lines and files in micro-average fashion.

_Character_ here does not mean Unicode codepoint, but glyph (i.e. combining characters are applied before any edit operations; this amounts to the `metric=Levenshtein` parameter of `cor-asv-ann-eval` and `ocrd-cor-asv-ann-evaluate`).

### Results

| **COR model** | **OCR model** | **dataset** | **#lines** | **CER OCR** | **CER COR** | **RT** | **comment** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| dta19.Fraktur4 | Fraktur4 | GT4HistOCR/dta19 | 19302 | 7.5 | 4.7 | 0.5 | in-domain |
| dta19.Fraktur4 | Fraktur4 | GT4HistOCR/dta19 | 4907 | 6.2 | 3.7 | 0.2 | in-domain |
| dta19.Fraktur4 | Fraktur4 | GT4HistOCR/dta19 | 4907 | 6.2 | 3.4 | 0.5 | in-domain |
| dta19.Fraktur4 | deu-frak3 | GT4HistOCR/dta19 | 4907 | 9.0 | 8.9 | 0.9 | out-domain |
| dta19.Fraktur4 | deu-frak3 | GT4HistOCR/dta19 | 4907 | 9.0 | 8.4 | 0.5 | out-domain |
| dta19.Fraktur4 | frk4 | GT4HistOCR/dta19 | 4907 | 6.5 | 5.6 | 0.9 | out-domain |
| dta19.Fraktur4 | frk4 | GT4HistOCR/dta19 | 4907 | 6.5 | 4.3 | 0.5 | out-domain |
| dta19.Fraktur4 | deu+frk4 | GT4HistOCR/dta19 | 4907 | 6.1 | 5.2 | 0.9 | out-domain |
| dta19.Fraktur4 | deu+frk4 | GT4HistOCR/dta19 | 4907 | 6.1 | 4.3 | 0.5 | out-domain |
| dta19.Fraktur4 | ocrofraktur | GT4HistOCR/dta19 | 4907 | 10.4 | 9.2 | 0.5 | out-domain |
| dta19.Fraktur4 | ocrofraktur-jze | GT4HistOCR/dta19 | 4907 | 7.4 | 6.6 | 0.5 | out-domain |
| dta19.Fraktur4 | GT4HistOCR | GT4HistOCR/dta19 | 4907 | 0.5 | 0.7 | 0.9 | out-domain |
| dta19.Fraktur4 | GT4HistOCR | GT4HistOCR/dta19 | 4907 | 0.5 | 1.0 | 0.5 | out-domain |
| dta19.Fraktur4 | Fraktur4 | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 15.7 | 15.8 | 0.9 | out-domain |
| | | | | | | | |
| pre19.Fraktur4 | Fraktur4 | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 15.7 | 15.1 | 0.2 | in-domain |
| pre19.Fraktur4 | Fraktur4 | GT4HistOCR/!dta19 | 5844 | 15.4 | 14.9 | 0.2 | in-domain |
| pre19.Fraktur4 | Fraktur4 | OCR-D<19 | 227 | 22.9 | 21.1 | 0.2 | in-domain |
| pre19.Fraktur4 | Fraktur4 | GT4HistOCR/dta19 | 4907 | 6.2 | 6.5 | 0.9 | out-domain |
| pre19.Fraktur4 | deu-frak3 | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 27.2 | 27.3 | 0.9 | out-domain |
| | | | | | | | |
| pre19.deu-frak3 | deu-frak3 | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 27.2 | 22.2 | 0.2 | in-domain |
| pre19.deu-frak3 | deu-frak3 | GT4HistOCR/dta19 | 4907 | 9.0 | 9.2 | 0.9 | out-domain |
| pre19.deu-frak3 | Fraktur4 | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 15.7 | 16.1 | 0.9 | out-domain |
| | | | | | | | |
| pre19.incunabula | incunabula | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 3.8 | 7.7 | 0.2 | in-domain |
| pre19.incunabula | incunabula | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 3.8 | 4.1 | 0.9 | in-domain |
| pre19.incunabula | incunabula | GT4HistOCR/!dta19 | 5844 | 1.8 | 2.0 | 0.9 | in-domain |
| | | | | | | | |
| pre19.latinhist | latinhist | GT4HistOCR/!dta19 + OCR-D<19 | 6071 | 34.2 | 26.8 | 0.2 | in-domain |
| | | | | | | | |
| gt4histocr.s-ſ | GT with `s/ſ/s/g` | GT4HistOCR | 56591 | 2.7 | 0.3 | 0.9 | in-domain |
| gt4histocr.s-ſ | deu-frak3 | GT4HistOCR | 10978 | 19.1 | 16.7 | 0.9 | out-domain |

### Discussion

As can be seen, performance on **out-domain** data is very poor (despite already adapting the rejection threshold). Rejection is limited here, because the model can fail to _find_ the rejection hypothesis when alignment is too bad already, and of course the model is blind towards its own errors.

Moreover, **in-domain** results show that the text cannot be reconstructed more than a certain extent (i.e. if OCR results are bad, then post-correction results will be as well, and vice versa; improvement is usually "absolute", not "relative"). Also, the `pre19` models are not nearly as good as the `dta19` models (for their respective task).

It remains to be investigated further, but it seems plausible that the key reasons for this are **quality/quantity of training data**. During the runtime of the project (2018-2019), there was:
- too little textual ground truth (and not representative), especially in the `pre19` category,
- no good baseline OCR model freely available (as is now, cf. [Tesseract models](https://github.com/tesseract-ocr/tesstrain/wiki) and [Calamari models](https://github.com/Calamari-OCR/calamari_models)),
- no OCR that could (correctly) share alternative hypotheses (besides `deu-frak3`).

The latter point could also be overcome by adding [multi-OCR alignment](http://www.ccs.neu.edu/home/dasmith/dong-smith-acl-2018.pdf). Fully utilising OCR hypotheses (from multiple engines or a single one) would also open the prospect of learning large generic models.
