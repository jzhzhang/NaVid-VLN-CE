
# NaVid 

**Video-Language in, Actions out! No odometry, Dpeth, or Map required!** This project contains the evaluation code of our RSS 2024 paper:

 **NaVid: Video-based VLM Plans the Next Step for Vision-and-Language Navigation**.

Contributors: [Jiazhao Zhang](https://jzhzhang.github.io/), Kunyu Wang, [Rongtao Xu](https://scholar.google.com.hk/citations?user=_IUq7ooAAAAJ), [Gengze Zhou](https://gengzezhou.github.io/), [Yicong Hong](https://yiconghong.me/), Xiaomeng Fang, [Qi Wu](http://qi-wu.me/), [Zhizheng Zhang](https://scholar.google.com/citations?user=X7M0I8kAAAAJ&hl=en), [He Wang](https://hughw19.github.io/)<br>

[[Paper & Appendices](https://arxiv.org/pdf/2402.15852)] [[Projece Page](https://pku-epic.github.io/NaVid/)]



https://github.com/user-attachments/assets/beb74d19-10f1-4a99-bb15-693731f2c2af



---

## Prerequisites 

### 1. Installation

To begin, you'll need to set up the [Habitat VLN-CE](https://github.com/jacobkrantz/VLN-CE) environment. The first step is to install [Habitat-sim (version 0.1.7)](https://github.com/facebookresearch/habitat-sim/tree/v0.1.7) with Python 3.8. We recommend using [Miniconda](https://docs.anaconda.com/miniconda/) or [Anaconda](https://www.anaconda.com/) for easy installation:


```
conda create -n vlnce_navid python=3.8
conda activate vlnce_navid
conda install -c aihabitat -c conda-forge habitat-sim=0.1.7 headless
```

Next, install [Haibtat-Lab 0.1.7](https://github.com/facebookresearch/habitat-lab/tree/v0.1.7):
```
mkdir navid_ws | cd navid_ws
git clone --branch v0.1.7 git@github.com:facebookresearch/habitat-lab.git
cd habitat-lab
# installs both habitat and habitat_baselines
python -m pip install -r requirements.txt
python -m pip install -r habitat_baselines/rl/requirements.txt
python -m pip install -r habitat_baselines/rl/ddppo/requirements.txt
python setup.py develop --all
```
Finally, install NaVid:
```
cd ..
git clone jzzhang@navid-vln-ce.git
cd NaVid-VLN-CE
pip install -r requrelments.txt
```

### 2. Vision-and-Language Data

Follow the instructions in the [VLN-CE Data Section](https://github.com/jacobkrantz/VLN-CE?tab=readme-ov-file#data) to set up the scene dataset and episodes dataset. After completing the data preparation, update the data location in [R2R config file](VLN_CE/habitat_extensions/config/vlnce_task_navid_r2r.yaml) and [RxR config file](VLN_CE/habitat_extensions/config/vlnce_task_navid_rxr.yaml). An example configuration is shown below, please modify the task files to align your data configuration:
```
NDTW:
  GT_PATH: data/datasets/R2R_VLNCE_v1-3_preprocessed/{split}/{split}_gt.json.gz 
DATASET:
  TYPE: VLN-CE-v1 # for R2R 
  SPLIT: val_unseen
  DATA_PATH: data/datasets/R2R_VLNCE_v1-3_preprocessed/{split}/{split}.json.gz # episodes dataset
  SCENES_DIR: data/scene_datasets/ # scene datasets 

```

### 3. Pretrained Weights
First download the pretriend weights for vision encoder [EVA-ViT-G](https://github.com/dvlab-research/LLaMA-VID/tree/main). Then, download the [finetuned NaVid model](https://huggingface.co/Jzzhang/NaVid/tree/main). The model has been trained on extensive samples from the `training splits` of the VLN-CE R2R and RxR datasets, following the training strategy of [Uni-NaVid](https://arxiv.org/pdf/2412.06224).


| Evaliation Benchmark |  TL  |  NE  |  OS  |  SR  |  SPL |
|----------------------|:----:|:----:|:----:|:----:|:----:|
| VLN-CE R2R Val.      | 10.7 | 5.65 | 49.2 | 41.9 | 36.5 |
| [VLN-CE R2R Test](https://eval.ai/web/challenges/challenge-page/719/leaderboard/1966)      | 11.3 | 5.39 |  52  |  45  |  39  |
| VLN-CE RxR Val.      | 15.4 | 5.72 | 55.6 | 45.7 | 38.2 |


### 4.  Structure
We recommend organizing your project directory as follows
```
navid_ws
├── habitat-lab
├── NaVid-VLN-CE
│   ├── navid
│   ├── VLN_CE
│   ├── model_zoo
│   │   ├── eva_vit_g.pth
│   │   ├── <navid_weights>
```


## Evaluation

To evaluate the model on multiple GPUs, use the provided evaluation `eval_navid_vlnce.sh` script. Each GPU will handle one split of all episodes. Before running the script, modify the environment variables as follows:

```
CHUNKS=8 # GPU numbers
MODEL_PATH="" # model wieght
CONFIG_PATH="" # task configuration configure, see script for an example
SAVE_PATH="" #  results
```

Run the script with:
```
bash eval_navid_vlnce.sh
```

Results will be saved in the specified `SAVE_PATH`, which will include a `log` directory and a `video` directory. To monitor the results during the evaluation process, run:

```
watch -n 1 python  analyze_results.py --path YOUR_RESULTS_PATH
```
To stop the evaluation, use:
```
bash kill_navid_eval.sh
```


## Citation
If you find this work useful for your research, please consider citing:
```
@article{zhang2024navid,
        title={NaVid: Video-based VLM Plans the Next Step for Vision-and-Language Navigation},
        author={Zhang, Jiazhao and Wang, Kunyu and Xu, Rongtao and Zhou, Gengze and Hong, Yicong and Fang, Xiaomeng and Wu, Qi and Zhang, Zhizheng and Wang, He},
        journal={Robotics: Science and Systems},
        year={2024}
      }
```

## Acknowledgments
Our code is based on [LLaMMA-VID](https://github.com/dvlab-research/LLaMA-VID) and [VLN-CE](https://github.com/jacobkrantz/VLN-CE). 

This is an open-source version of NaVid, some functions have been rewritten to avoid certain license. 

If you have any questions, feel free to email Jiazhao Zhang at zhngjizh@gmail.com.