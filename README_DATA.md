# Data

This README provides guidelines on how to structure and prepare aligned multimodal training data from multiple datasets. We also provide the processed [EgoGen](https://ego-gen.github.io) dataset for better understanding. 

## Dataset format and structure

### Modified WebDataset format (for larger datasets and/or cloud storage)

We recommend organizing training data using a modified version of the [WebDataset format](https://github.com/webdataset/webdataset). With this format, the dataset is split into tarfiles, with each tarfile containing 1'000 samples from one modality (e.g., RGB video, gaze dynamics, etc.). This data can be stored either on a local disk or in common cloud object stores like S3. Storing the data as tarfiles reduces the number of object read requests when streaming directly from cloud buckets. The data is organized as such:

```
root/modality_a/dataset_a/shard-00000.tar
root/modality_a/dataset_a/shard-00001.tar
root/modality_a/dataset_a/shard-00002.tar

root/modality_a/dataset_b/shard-00000.tar
root/modality_a/dataset_b/shard-00001.tar
root/modality_a/dataset_b/shard-00002.tar

root/modality_b/dataset_a/shard-00000.tar
root/modality_b/dataset_a/shard-00001.tar
root/modality_b/dataset_a/shard-00002.tar
```

Here, `modality_a` and `modality_b` are placeholders for the names of the modalities (e.g., `rgb`, `cam`, or more specific modality names for dataset versioning).

For example, you can save EgoGen data to paths like `
'/iopsstor/scratch/cscs/lgen/main_model_token/[rgb,depth,cam]/egogen/token/shard-{000000..000040}.tar'` as described [here](cfgs/default/egom2p/data/ego/main/mix_mod4_all2all_2048.yaml).

Each tarfile expands into individual files with arbitrary names but the same extension, such as:
```
xxx.ext
xxy.ext
xxz.ext
```

The file extension varies depending on the modality (e.g., `.mp4` for RGB videos, `.npz` for pre-computed tokens, etc.). EgoM2P datasets only include pre-computed tokens.

To load aligned samples from all modalities, make sure that **filenames are identical for all modalities, except for the modality name and file extensions**. Shards should also be **ordered numerically** (as shown above) to support brace-expand notation. New modalities can be easily added by creating a new folder with tarfiles in the same directory. Existing modalities can also be modified by updating their specific tarfiles or creating new ones.

### Simple hierarchical format (for smaller local datasets)

For smaller datasets that can be stored locally, we also support a simpler hierarchical structure. This is convenient for datasets like validation sets or transfer datasets. The data structure is as follows:

```
root/modality_a/dataset_a/xxx.ext
root/modality_a/dataset_a/xxy.ext
root/modality_a/dataset_b/xxz.ext

root/modality_b/dataset_a/xxx.ext
root/modality_b/dataset_b/xxy.ext
root/modality_b/dataset_c/xxz.ext
```

The folder and file names can be arbitrary as long as they are aligned across modalities.

## Datasets

We use the following datasets to train and/or evaluate EgoM2P models.
For pre-training:
- [**EgoGen**](https://ego-gen.github.io)
- [**HoloAssist**](https://holoassist.github.io/)
- [**EgoExo4D**](https://ego-exo4d-data.org/)
- [**HOT3D (aria)**](https://www.projectaria.com/datasets/hot3D/)
- [**HOT3D (quest)**](https://www.projectaria.com/datasets/hot3D/)
- [**H2O**](https://taeinkwon.com/projects/h2o/)
- [**TACO**](https://taco2024.github.io/)
- [**ARCTIC**](https://arctic.is.tue.mpg.de/)

For post-training and evaluations:
- [**HOI4D**](https://hoi4d.github.io/)
- [**ASE**](https://www.projectaria.com/datasets/ase/)
- [**ADT**](https://www.projectaria.com/datasets/adt/)


Please refer to their respective pages for instructions on how to download them and license information.

### EgoGen download instructions

Download data from [here](https://egogen.ethz.ch/). You will need to first register yourself, and use the received temporary username and password for the download. 

- egogen_for_egom2p.tar (1.93 GB) is the processed token we used in the pretraining. You can organize your dataset like this folder.
- egom2p_*_ori.tar are the original EgoGen dataset. It contains RGB, depth and camera trajectories saved using scripts similar to [this](https://github.com/ligengen/EgoGen/blob/main/experiments/gen_egobody_depth.py).

### Data processing

We provided many useful functions in [gen_aligned_training_data.py](gen_aligned_training_data.py). They might be useful to process your own dataset.

## Pseudo labeling

There is no effective pseudo labelers for all missing modalities in egocentric vision. We only use [RollingDepth](https://rollingdepth.github.io/) to pseudo label missing depth data. Please refer to its respective pages for inference instructions and license information.

| Modality              | Model                                | Homepage                                                                                             |
|-----------------------|--------------------------------------|------------------------------------------------------------------------------------------------------|
| Depth                 |     Rolling Depth       | [link](https://rollingdepth.github.io/)                               |


## Pre-tokenization

During training, all modalities are tokenized to discrete tokens using modality-specific tokenizers. Please refer to [README_TOKENIZATION.md](README_TOKENIZATION.md) for more information. To avoid dataloading and tokenization from becoming a training bottleneck, we instead pre-compute the tokens of all modalities and then directly load the tokens.

