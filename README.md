# nomercy-lyricsalign-models

Pre-built phoneme model in GGUF format for the `lyricsalign` audio filter of
[nomercy-ffmpeg](https://github.com/NoMercy-Entertainment/nomercy-ffmpeg)
(issue #126): known lyric text placed on a vocal stem by CTC forced alignment.

Every release carries the model file and its `.sha256` sidecar. The filter
takes the file's path in its `model` option; the vocabulary and every layer
size are read from the file.

## Model

| file | architecture | source | licence |
|---|---|---|---|
| `wav2vec2-xlsr-53-espeak-cv-ft-f16.gguf` | `wav2vec2-ctc`: Wav2Vec2ForCTC large (24 layers, 16 heads, FFN 4096), 392 IPA phones, f16 weights (about 630 MB); the filter computes in f32 | [facebook/wav2vec2-xlsr-53-espeak-cv-ft](https://huggingface.co/facebook/wav2vec2-xlsr-53-espeak-cv-ft) (Xu, Baevski, Auli 2021, *Simple and Effective Zero-shot Cross-lingual Phoneme Recognition*) | Apache-2.0 (Meta AI) |

## How a release is built

`tools/wav2vec2-gguf` in nomercy-ffmpeg converts the checkpoint
(`python -I convert.py <checkpoint_dir> <out.gguf>`, or its Docker image) and
verifies the written file: every tensor by name, shape and type, the metadata,
and the folded weight norm of the positional convolution. The conversion is
deterministic: the same checkpoint gives the same bytes, so the digest below
can be reproduced.

```
b1474de65e4a966046d390f191af092e718e5e773dcedc5398f31057ee2d38b4  wav2vec2-xlsr-53-espeak-cv-ft-f16.gguf
```

The nomercy-ffmpeg CI fetches the release asset by this digest
(`tests/lib/lyricsalign-assets.sh`) and runs the JFK port gates on it: the
port's emissions within 0.05 nats of the prototype's and every word of the
JFK clip within 40 ms.

A signed `manifest.json` for the media server's model download (the way the
whisper and stemsplit model releases carry one) is added when the media server
learns to download this model (nomercy-media-server#620 follow-up).
