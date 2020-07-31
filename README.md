# Norm-in-Norm Loss with Faster Convergence and Better Performance for Image Quality Assessment
[![License](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](License)

## Description
LinearityIQA code for the following paper:

- Dingquan Li, Tingting Jiang, and Ming Jiang. [Norm-in-Norm Loss with Faster Convergence and Better Performance for Image Quality Assessment](). In Proceedings of the 28th ACM International Conference on Multimedia (MM ’20), October 12-16, 2020, Seattle, WA, USA. [[arxiv version]]() **Coming soon**

![Norm-in-Norm Loss](norm-in-norm-loss.jpg)
![Framework](framework.jpg)

## How to?
### Install Requirements
```bash
conda create -n reproducibleresearch pip python=3.6
source activate reproducibleresearch
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
source deactive
```

### Download Datasets
Download the [KonIQ-10k](http://database.mmsp-kn.de/koniq-10k-database.html) and [CLIVE](https://live.ece.utexas.edu/research/ChallengeDB/index.html) datasets. Then, run the following `ln` commands in the root of the repo.
```bash
ln -s KonIQ-10k_path KonIQ-10k # KonIQ-10k_path is your path to the KonIQ-10k dataset
ln -s CLIVE_path CLIVE # CLIVE_path is your path to the CLIVE dataset
```

### Training on KonIQ-10k
```bash
CUDA_VISIBLE_DEVICES=0 python main.py --dataset KonIQ-10k --resize --exp_id 0 --lr 1e-4 -bs 8 -e 30 --ft_lr_ratio 0.1 --arch resnext101_32x8d --loss_type Lp --p 1 --q 2 > exp_id=0-resnext101_32x8d-p=1-q=2-664x498.log 2>&1 & # The saved checkpoint is copied and renamed as "p1q2.pth". 
CUDA_VISIBLE_DEVICES=1 python main.py --dataset KonIQ-10k --resize --exp_id 0 --lr 1e-4 -bs 8 -e 30 --ft_lr_ratio 0.1 --arch resnext101_32x8d --loss_type Lp --p 1 --q 2 --alpha 1 0.1 > exp_id=0-resnext101_32x8d-p=1-q=2-alpha=1,0.1-664x498.log 2>&1 & # The saved checkpoint is copied and renamed as "p1q2plus0.1variant.pth"
```
More options can be seen by running the help command `python main.py --help`.
#### Visualization
```bash
tensorboard --logdir=logs --port=6006 # in the server (host:port)
ssh -p port -L 6006:localhost:6006 user@host # in your PC. See the visualization in your PC
```

You can download our [checkpoints](https://pan.baidu.com/s/1MRamimHWX8F-SOQ_QsIrvg) with a password `4z7z`. Then paste it to `checkpoints/`.


### Testing
#### Testing on KonIQ-10k test set (Intra Dataset Evaluation)
```bash
CUDA_VISIBLE_DEVICES=0 python test_dataset.py --dataset KonIQ-10k --resize --arch resnext101_32x8d --trained_model_file checkpoints/p1q2.pth
CUDA_VISIBLE_DEVICES=1 python test_dataset.py --dataset KonIQ-10k --resize --arch resnext101_32x8d --trained_model_file checkpoints/p1q2plus0.1variant.pth
```
#### Testing on CLIVE (Cross Dataset Evaluation)
```bash
CUDA_VISIBLE_DEVICES=0 python test_dataset.py --dataset CLIVE --resize --arch resnext101_32x8d --trained_model_file checkpoints/p1q2.pth
CUDA_VISIBLE_DEVICES=1 python test_dataset.py --dataset CLIVE --resize --arch resnext101_32x8d --trained_model_file checkpoints/p1q2plus0.1variant.pth
```
#### Test Demo
```bash
CUDA_VISIBLE_DEVICES=0 python test_demo.py --img data/1000.JPG --resize --arch resnext101_32x8d --trained_model_file checkpoints/p1q2.pth
# > The image quality score is 10.430044178601875
```

### Remark
If one wants to use the "Norm-in=Norm" loss in his project, he can refer to the `norm_loss_with_normalization` in `IQAloss.py`.

If one wants to use the model in his project, he can refer to the `IQAmodel.py`.

### Contact
Dingquan Li, dingquanli AT pku DOT edu DOT cn.