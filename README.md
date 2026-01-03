# EgoM2P: Egocentric Multimodal Multitask Pretraining

[`Website`](https://egom2p.github.io) | [`BibTeX`](#citation)  | [`Paper`](https://www.arxiv.org/abs/2506.07886)

Official implementation and pre-trained models for :

[**EgoM2P: Egocentric Multimodal Multitask Pretraining**](https://www.arxiv.org/abs/2506.07886), ICCV 2025 <br>
*[Gen Li](https://ligengen.github.io), [Yutong Chen](https://vlg.inf.ethz.ch/team/Yutong-Chen.html)\*, [Yiqian Wu](https://onethousandwu.com/)\*, [Kaifeng Zhao](https://zkf1997.github.io/)\*, [Marc Pollefeys](https://cvg.ethz.ch/team/Prof-Dr-Marc-Pollefeys), [Siyu Tang](https://vlg.inf.ethz.ch/team/Prof-Dr-Siyu-Tang.html)*

<br>

![egom2p main figure](https://egom2p.github.io/images/teaser.png)

EgoM2P: A large-scale egocentric multimodal and multitask model, pretrained on eight extensive egocentric datasets. It incorporates four modalities—RGB and depth video, gaze dynamics, and camera trajectories—to handle challenging tasks like monocular egocentric depth estimation, camera tracking, gaze estimation, and conditional egocentric video synthesis. For simplicity, we only visualize four frames here.

## Table of contents
- [Usage](#usage)
    - [Installation](#installation)
    - [Data](#data)
    - [Tokenization](#tokenization)
    - [EgoM2P Training](#egom2p-training)
    - [Inference](#inference)
- [Pretrained models](#pretrained-models)
    - [EgoM2P model](#egom2p-model)
    - [Tokenizers](#tokenizers)
- [License](#license)
- [Citation](#citation)

## Usage

### Installation
We provide two installation methods:

1. Container for aarch64 (our model was trained and tested using this method):
First follow the environment setup in this Swiss-AI [tutorial](https://docs.cscs.ch/tutorials/ml/llm-inference/#set-up-a-python-virtual-environment). Please finish all steps until this [section](https://docs.cscs.ch/tutorials/ml/llm-inference/#set-up-a-python-virtual-environment). Please refer to the provided [Dockerfile](./Dockerfile) for reference.
```
# Before installing, edit req.txt and remove the lines "torch==2.6.0" and "decord==0.6.0"!
pip install -r req.txt
```
You still need to install [decord](https://github.com/dmlc/decord?tab=readme-ov-file#install-from-source) from source since there is no precompiled version for linux-aarch64.

2. Conda environment for x86-64 (not tested for multi-node training):
```
conda create -n egom2p python=3.12.3 -y
conda activate egom2p
pip install -r req.txt
```

### Data  

See [README_DATA.md](README_DATA.md) for instructions on how to prepare aligned multimodal datasets.

### Tokenization  

See [README_TOKENIZATION.md](README_TOKENIZATION.md) for instructions on how to train modality-specific tokenizers.

### EgoM2P Training

See [README_TRAINING.md](README_TRAINING.md) for instructions on how to train EgoM2P models.

### Inference

See [README_INFERENCE.md](README_INFERENCE.md) for instructions on how to perform inference. 


## Pretrained models

### EgoM2P model

| Model   | # Mod. | Datasets | # Params | Config | Weights         |
| ------- | ------ | -------- | -------- | ------ | --------------- |
| EgoM2P | 4 | [Eight egocentric datasets](README_DATA.md) | 400M (incl. embedding layers) | [Config](cfgs/default/egom2p/models/main/ego-b_mod4_500b_clariden_2048_camcv_depthdenoise.yaml) | [Checkpoint](https://polybox.ethz.ch/index.php/s/3byirHr9ka7gs8W) |

We tried to scale the model size further but due to limited dataset size, this will leave as promising future work.

### Tokenizers

EgoM2P handles clips with 2 second length. Video modalities are downsampled to 8 fps from 30 fps, and non-video modalities are at 30 fps.

| Modality                   | Shape | Number of tokens | Codebook size   |  Weights |
|----------------------------|------------|------------------|-----------------|--------------|
| Camera Trajectory                        | 60x9    | 30          | 256          | [Checkpoint](https://polybox.ethz.ch/index.php/s/eqcEQNkZ7e8iQs3)  |
| Gaze Dynamics                      | 60x2    | 30          |  256              | [Checkpoint](https://polybox.ethz.ch/index.php/s/bbtqrjbZicg28fN) |

We use recent pretrained Cosmos Tokenizers for video modalities:

| Modality                   | Shape | Number of tokens | Codebook size   |  Weights |
|----------------------------|------------|------------------|-----------------|--------------|
| RGB/Depth Video                        | 16x256x256    | 5120          | 64k          | Download encoder.jit and decoder.jit from [Checkpoint](https://huggingface.co/nvidia/Cosmos-0.1-Tokenizer-DV4x8x8)  |

We observed that Cosmos Tokenizers do not work well for low fps and low resolution videos (however usually video models need to train from low resolutions first). Feel free to use other video tokenizers and retrain EgoM2P:)

## License

EgoM2P builds on [4M](https://4m.epfl.ch), a multimodal image foundation model, by extending it to video and motion domains. We acknowledge 4M and its [acknowledgements](ACKNOWLEDGEMENTS.md). While most of the dependencies are not used in EgoM2P, we choose to keep some unused 4M code in our codebase for possible extensions.

We follow its license. The code in this repository is released under the Apache 2.0 license as found in the [LICENSE](LICENSE) file.

The model weights in this repository are released under the Sample Code license as found in the [LICENSE_WEIGHTS](LICENSE_WEIGHTS) file.

## Citation

If you find this repository helpful, please consider citing our work:
```
@InProceedings{Li_2025_ICCV,
    author    = {Li, Gen and Chen, Yutong and Wu, Yiqian and Zhao, Kaifeng and Pollefeys, Marc and Tang, Siyu},
    title     = {EgoM2P: Egocentric Multimodal Multitask Pretraining},
    booktitle = {Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV)},
    month     = {October},
    year      = {2025},
    pages     = {10830-10843}
}

@InProceedings{li2024egogen,
    author    = {Li, Gen and Zhao, Kaifeng and Zhang, Siwei and Lyu, Xiaozhong and Dusmanu, Mihai and Zhang, Yan and Pollefeys, Marc and Tang, Siyu},
    title     = {EgoGen: An Egocentric Synthetic Data Generator},
    booktitle = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month     = {June},
    year      = {2024},
    pages     = {14497-14509}
}
```
