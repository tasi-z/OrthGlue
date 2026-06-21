# OrthGlue

**OrthGlue: A Multi-Scale Attention-Based Orthogonal Feature Fusion Network for Remote Sensing Image Matching** is a sparse local-feature matcher for remote sensing image registration. It uses SuperPoint features and a dual-branch attention design:

- **Global Aggregation (GA)** for image-wide context.
- **Seed Aggregation (SA)** for reliable anchor-based local propagation.
- **Orthogonal Fusion (OF)** to reduce redundancy and preserve complementary information.

The main matcher is [gluefactory/models/matchers/orthglue.py](gluefactory/models/matchers/orthglue.py).

## Structure

```text
.
|-- gluefactory/
|   |-- configs/                      # packaged configs
|   |   `-- superpoint+orthglue_megadepth.yaml
|   |-- datasets/                     # dataset loaders
|   |-- eval/                         # benchmark pipelines
|   |   `-- WHU/                      # WHU evaluation variants
|   |-- geometry/                     # homography/epipolar/depth utilities
|   |-- models/
|   |   |-- matchers/orthglue.py      # OrthGlue matcher
|   |   `-- two_view_pipeline.py      # extractor + matcher pipeline
|   |-- robust_estimators/            # robust pose/homography estimators
|   `-- utils/                        # image, prediction export, experiment utilities
`-- gluefactory_nonfree/superpoint.py # SuperPoint wrapper
```

## Paths

The code uses these environment variables:

```bash
export SGAT_DATA_PATH=/path/to/data
export SGAT_WEIGHTS_PATH=/path/to/weights
export SGAT_EXP_ROOT=/path/to/outputs
```

Defaults are `./data`, `./weights`, and `./outputs`.

## Data

Homography datasets should be placed under `SGAT_DATA_PATH` with this format:

```text
pair_000001/
|-- 1.png
|-- 2.png
`-- H.npy
```

`H.npy` is a `3 x 3` homography from `1.png` to `2.png`. Default evaluation paths:



## Weights

Default SuperPoint path: `weights/public/superpoint_extractor_public.pth`. Pass OrthGlue weights with:

```bash
model.matcher.weights=/path/to/orthglue_matcher.pth
```

## Evaluation

Run from the repository root with `model.matcher.name=matchers.orthglue`:

```bash
python -m gluefactory.eval.GoogleEarth --conf configs/superpoint+SGAT.yaml --tag orthglue_googleearth model.matcher.name=matchers.orthglue --overwrite
python -m gluefactory.eval.WHU.WHU_rgb_rgb --conf configs/superpoint+SGAT.yaml --tag orthglue_whu_rgb_rgb model.matcher.name=matchers.orthglue --overwrite
python -m gluefactory.eval.WHU.WHU_sar_sar --conf configs/superpoint+SGAT.yaml --tag orthglue_whu_sar_sar model.matcher.name=matchers.orthglue --overwrite
```

Other WHU entry points: `WHU_rgb_sar`, `WHU_rgb_nirs`, `WHU_nirs_nirs`, `WHU_nirs_sar`. Results are saved to `$SGAT_EXP_ROOT/results/<benchmark>/<tag>/`.

## Configs

- Main config: [configs/superpoint+SGAT.yaml](configs/superpoint+SGAT.yaml)
- MegaDepth config: [gluefactory/configs/superpoint+orthglue_megadepth.yaml](gluefactory/configs/superpoint+orthglue_megadepth.yaml)
- Matcher module: `matchers.orthglue`

No top-level training launcher is included; reuse `TwoViewPipeline`, dataset loaders, and the loss in `orthglue.py`. Internal names such as `SGAT` or `MSGA` remain for compatibility and refer to OrthGlue here.

## Citation

```bibtex
@article{orthglue,
  title = {OrthGlue: A Multi-Scale Attention-Based Orthogonal Feature Fusion Network for Remote Sensing Image Matching},
  author = {OrthGlue authors},
  journal = {Information Sciences},
  year = {2026}
}
```
