# cor-asv-ann-models

Pretrained models for the ANN-based post-correction component.

## Installation

To checkout the models, [`Git LFS` must be installed](https://github.com/git-lfs/git-lfs/releases).

## Models

### OCR post-correction

All models use `cor-asv-ann-train --width 512 --depth 2` and were initialised with weights from a [language model](https://github.com/OCR-D/ocrd_keraslm) via `--init-model`, then pretrained on 200k lines of clean text (input=output) from [DTA](https://www.deutsches-textarchiv.de), and then retrained via `--load-model` on [GT4HistOCR](https://zenodo.org/record/1344132/export/hx) and [OCR-D GT](http://www.ocr-d.de/gt), processed by various OCR models (input=OCR with confidence, output=GT). The latter is allowed to change _all_ weights (not just fine-tuning) and does _not_ reset the encoder layer weights (`--reset-encoder`).

- [dta19.Fraktur4](s2s.dta19.Fraktur4.d2.w0512.adam.attention.stateless.variational-dropout.char.pretrained+retrained-conf.h5): on 19th century Fraktur texts (GT4HistOCR/corpus/dta19) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `script/Fraktur`
- [pre19.Fraktur4](s2s.pre19.Fraktur4.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `script/Fraktur`
- [pre19.deu-frak3](s2s.pre19.deu-frak3.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 3 model](https://github.com/tesseract-ocr/tessdata) `deu-frak`
- [pre19.Latin4](s2s.pre19.Latin4.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `script/Latin`
- [pre19.deu4](s2s.pre19.deu4.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Tesseract 4 model](https://github.com/tesseract-ocr/tessdata_best) `deu`
- [pre19.incunabula](s2s.pre19.incunabula.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Ocropus 1](https://github.com/tmbdev/ocropy) model `incunabula.pyrnn` included in GT4HistOCR
- [pre19.latinhist](s2s.pre19.latinhist.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.pretrained+retrained-conf.h5): on 15-18th century blackletter texts (GT4HistOCR others, OCR-D) for the [Ocropus 1](https://github.com/tmbdev/ocropy) model `latinhist.pyrnn` included in GT4HistOCR

### Other

- [gt4histocr.s-ſ](s2s.gt4histocr.s-ſ.d2.w0512.adam.attention.stateless.variational-dropout.char.transfer-lm.h5): on GT4HistOCR ground truth degraded by replacing `ſ` into `s` in the input (encouraging the network to learn its reconstruction)

## Evaluation

...

