# Hybrid Knowledge Routed Network for Large-scale Object Detection 

This repository is written by Chenhan Jiang and Hang Xu interned at SenseTime

Code for reproducing the results in the following paper, and large part code is reference from [jwyang/faster-rcnn.pytorch](https://github.com/jwyang/faster-rcnn.pytorch):

[**Hybrid Knowledge Routed Modules for Large-scale Object Detection**]()

Chenhan Jiang, Hang Xu, Xiaodan Liang, Liang Lin

*Neural Information Processing Systems (NIPS), 2018*

## Results



## Getting Started

Clone the repo:

```
https://github.com/chanyn/HKRM.git
```

### Requirements

+ python packages

  + PyTorch = 0.3.1
    
    *This project can not support pytorch 0.4, higher version will not recur results.*

  + Torchvision >= 0.2.0

  + cython

  + pyyaml

  + easydict

  + opencv-python

  + matplotlib

  + numpy

  + scipy

  + tensorboardX

    You can install above package using ```pip```:

    ```sh
    pip install Cython easydict matplotlib opencv-python pyyaml scipy
    ```

+ CUDA 8.0

+ gcc >= 4.9



### Compilation

Compile the CUDA dependencies:

```sh
cd {repo_root}/lib
sh make.sh
```

It will compile all the modules you need, including NMS, ROI_Pooing, ROI_Crop and ROI_Align. 



### Data Preparation

Create a data folder under the repo,

```sh
cd {repo_root}
mkdir data
```

+ **Prior Knowledge**: We instantiate attribute and relation as explicit knowledge from Visual Genome. We provide the [link](https://pan.baidu.com/s/1eHQQlYvbhSFxJq1nsnLv3Q) to download VG graph and other graph. We also provide the [frequency statistic](https://pan.baidu.com/s/1ScXaiQiWlZhuErhh3gNcag) from VG and procession code in ```lib/dataset/tool/compute_prior.py``` .  So You can transfer prior knowledge to other datasets. 

+ **ADE**: We provide [ADE20K](http://groups.csail.mit.edu/vision/datasets/ADE20K/) as an example.

  ```shell
  mkdir -p data/ADE
  cd data/ADE
  wget -v http://groups.csail.mit.edu/vision/datasets/ADE20K/ADE20K_2016_07_26.zip
  tar -xzvf ADE20K_2016_07_26.zip
  mv ADE20K_2016_07_26/* ./
  rmdir ADE20K_2016_07_26
  # then get the list of overlap with VG and train/test split
  wget -v http://.../ADE_split.tar.gz
  tar -xzvf ADE_split.tar.gz
  rm -vf ADE_split.tar.gz
  cd ../..
  ```

+ **Visual Genome**: Download the VG images and annotations from [Visual Genome](http://visualgenome.org/). We use synset as label rather than name, and choose top 1000 and 3000 frequent classes. Our JSON datasets can be download from:

  [VG1000: Train and Test Set for Top 1000 Frequent Classes](https://pan.baidu.com/s/1t0BxE348vAjbHuYR-wy7Sg)

  [VG3000: Train and Test Sets for Top 3000 Frequent Classes](https://pan.baidu.com/s/1Mv5vVx0l1ikA9l1EjwCBwA)



## Training 

We used [ResNet101](https://www.dropbox.com/s/iev3tkbz5wyyuz9/resnet101_caffe.pth?dl=0) pretrained model on ImageNet in our experiments. Download it and put it into the data/pretrained_model/.

For example, to train the baseline with res101 on VG, simply run:

```shell
CUDA_VISIBLE_DEVICES=$GPU_ID python trainval_baseline.py \
                  --dataset vg --bs $BATCH_SIZE --nw $WORKER_NUMBER \
                  --log_dir $LOG_DIR --save_dir $WHERE_YOU_WANT
```

where 'bs' is the batch size with default 2, 'log_dir' is the location to save your tensorboard of train, 'save_dir' is your path to save model. 

Change dataset to 'ade' or 'coco' if you want to train on ADE or COCO.  If you have multi-GPUs, you should add '--mGPUs'.

To train our HKRM with res101 on VG:

```shell
CUDA_VISIBLE_DEVICES=$GPU_ID python trainval_HKRM.py \
                  --dataset vg --bs $BATCH_SIZE --nw $WORKER_NUMBER \
                  --log_dir $LOG_DIR --save_dir $WHERE_YOU_WANT \
                  --init --net HKRM --attr_size 256 --rela_size 256 --spat_size 256
```

where 'net' you can choose in ('HKRM', 'Attribute', 'Relation', 'Spatial'), and you should put [pretrained baseline model](https://pan.baidu.com/s/1L65QTvs0CWO9nxc6QMWuFw) into 'save_dir'.


## Testing

If you want to evaluate the detection performance of a pre-trained model on VG test set, simply run:

```shell
python test_net.py --dataset vg --net HKRM \
                   --load_dir $YOUR_SAVE_DIR \
                   --checksession $SESSION --checkepoch $EPOCH --checkpoint $CHECKPOINT 
```

Specify the specific model session, chechepoch and checkpoint, e.g., SESSION=325, EPOCH=12, CHECKPOINT=21985. And we provide the final model that you can load from [trained_model_hkrm](https://pan.baidu.com/s/1KcKY7TcHm8LQ3ZD3B3y3jQ).



## Citation

```
@inproceedings{jiangNIPS18hybrid,
    Author = {Chenhan Jiang and Hang Xu and Xiaodan Liang and Liang Lin},
    Title = {Hybrid Knowledge Routed Modules for Large-scale Object Detection},
    Booktitle = {Advances in Neural Information Processing Systems ({NIPS})},
    Year = {2018}
}
```



## Contact

If you have any questions about this repo, please feel free to contact [jchcyan@gmail.com](mailto:jchcya@gmail.com).



