# Rate-Accuracy Trade-Off In Video Classification With Deep Convolutional Neural Networks

## Introduction

This repository contains our public tool for producing cropped AVC and HEVC bitstream, extracting optical flow approximation (MVs), and two classification architectures (2D-CNN and 3D-CNN).

## Prerequisites

In order to run thie code you will need:
1. Python 2.7 
2. ffmpeg (tested with version 2.8.15)
3. TensorFlow (tested with TensorFlow 1.1.0)

## Producing Cropped AVC/H.264 Bitstream and Optical Flow Approximation (MVs)
To produce AVC/H.264 cropped bitstream and the approximated flow run
...
./JM_Cropped_MV_Stats -svid <source video>
...

The JM_Cropped_MV_Stats script takes an avi input video to produce the cropped bitsream and flow estimates according to the following steps:
1. use ffmpeg to transcode the input video to YUV format with size WxH
2. encoded YUV video using the modified AVC/H.264 JM refernce software to produced the cropped x264 bitsream
3. decoded the cropped bitstream using the modified AVC/H.264 JM reference software to extract the MVs
4. map these MVs to a Gird with a dimmension set according to the resolution of the input video (WxH) and res parameters

Various encoding parameters can be set directly such as
Option | Description
---|---
ecfg |  JM configuration file
svid |  input video
qp  |   QP value for P and I frames
sr  |   search range
res  |  resolution of the MB Grid (4,8,16)
   
Outputs:
1. Cropped JM bitstream
2. MV.bin file where MV values are mapped to a grid according to the correspodning MB position
3. States (including rate) per frame for the cropped bitstream
4. Summary of states for the cropped bitstream
5. States (including rate) per frame for the bitstream produced by the original JM encoder
6. Summary of states for the original JM bitstream

Sample Output:
%########################################################################################
JM config file=encoder_option2.cfg, Source Video=v_BoxingPunchingBag_g05_c01.avi, QP=40, Search Range=16, MV Resolution=8
%########################################################################################
Converting source video to YUV format
Producing cropped H.264 bitstream (encoder)
Extracting MVs from the cropped H.264 bitstream (decoder)
Original JM: encoding to produce rate (bps) per frame (optional) 
Mapping MV to a grid according to Macroblocks positions
Moving outputs to JMMV parent directory


The MVX tool takes an x264 bitstream as input and outputs the approximated flow 
and selectively decoded frames.

Install dependencies, if you are using a linux machine run:

```
apt-get update
apt-get install libav-tools libavutil-dev ffmpeg libboost-all-dev
```

Compile MVX using the provided Makefile:

```
make
```

Transcode your input video to be encoded using the GBR colorspace:

```
ffmpeg -y -i sample.mp4 -c:v libx264rgb -b:v 512k -bf 0 -pix_fmt rgb24  -r 25 -strict -2 gbr_sample.mp4
```

To extract the approximated flow along with GBR texture at active regions within frames, use:

```
./mvx -w 1 -r 10 -t 0 --rgb out.rgb --mv out.mv gbr_sample.mp4
```

The table below details what each parameter specifies:

Option | Description
--- | ---
-w | RGB texture writing interval
-r | reference frame caching interval
-t | threshold for writing RGB texture for frames in between reference frames
-8 | set motion vector map resolution to 8 pixels (default is 16)
--rgb  | frame texture output path
--mv | motion vector output path

Please have a look at our paper to have a better idea of how these parameters affect the classifier performance.

## Training
In order to train our model on UCF-101 (split 1) on a single GPU:

1. Convert the train motion vector bins by running:
```
python convert_UCF_bin.py --data_dir=<path to motion vector data dir (ucf_8x8_w10_r1_bin)> --out_dir=<path to output dir> --path_to_file_list=<path to train split 1 file list (ucfTrainTestlist/trainlist01.txt)>
```
2. Train on the converted bins:
```
python train.py --data_dir=<path to converted bins> --save_dir=<path to save checkpoints/summaries>
```

Summary graphs can be viewed using TensorBoard, using the following command in Terminal:
```
tensorboard --logdir=<path to saved checkpoints/summaries>
```

## Testing
In order to test our model on UCF-101 (split 1) on a single GPU:

1. Convert the test motion vector bins by running:
```
python convert_UCF_bin.py --data_dir=<path to motion vector data dir --out_dir=<path to output dir> --path_to_file_list=<path to test split 1 file list (ucfTrainTestlist/testlist01.txt)>
```
2. Test on the converted bins:
```
python test.py --data_dir=<path to converted bins> --save_dir=<path to saved checkpoints/summaries>
```


# Video Classification With CNNs: Using The Codec As A Spatio-Temporal Activity Sensor

## Introduction

This repository contains our public tool for selective decoding and flow approximation (MVX) and our temporal stream 3D-CNN classifier. Please find the details in our paper:

> *Video Classification With CNNs: Using The Codec As A Spatio-Temporal Activity Sensor*, 
> Chadha A., Abbas A., Andreopoulos Y., 
> [[arXiv](https://arxiv.org/abs/1710.05112)]

If you use the tools provided in this repository, please cite our work:
```
@article{chadha2017video,
  title={Video Classification With CNNs: Using The Codec As A Spatio-Temporal Activity Sensor},
  author={Chadha, Aaron and Abbas, Alhabib and Andreopoulos, Yiannis},
  journal={IEEE Transactions on Circuits and Systems for Video Technology},
  year={2017},
  publisher={IEEE}
}
```

## Prerequisites

In order to run thie code you will need:
1. AVLib (https://github.com/libav/libav)
2. Python 2.7 
3. TensorFlow (tested with TensorFlow 1.1.0)

## Compiling MVX And Preparing Video Inputs

The MVX tool takes an x264 bitstream as input and outputs the approximated flow 
and selectively decoded frames.

Install dependencies, if you are using a linux machine run:

```
apt-get update
apt-get install libav-tools libavutil-dev ffmpeg libboost-all-dev
```

Compile MVX using the provided Makefile:

```
make
```

Transcode your input video to be encoded using the GBR colorspace:

```
ffmpeg -y -i sample.mp4 -c:v libx264rgb -b:v 512k -bf 0 -pix_fmt rgb24  -r 25 -strict -2 gbr_sample.mp4
```

To extract the approximated flow along with GBR texture at active regions within frames, use:

```
./mvx -w 1 -r 10 -t 0 --rgb out.rgb --mv out.mv gbr_sample.mp4
```

The table below details what each parameter specifies:

Option | Description
--- | ---
-w | RGB texture writing interval
-r | reference frame caching interval
-t | threshold for writing RGB texture for frames in between reference frames
-8 | set motion vector map resolution to 8 pixels (default is 16)
--rgb  | frame texture output path
--mv | motion vector output path

Please have a look at our paper to have a better idea of how these parameters affect the classifier performance.

## Training
In order to train our model on UCF-101 (split 1) on a single GPU:

1. Convert the train motion vector bins by running:
```
python convert_UCF_bin.py --data_dir=<path to motion vector data dir (ucf_8x8_w10_r1_bin)> --out_dir=<path to output dir> --path_to_file_list=<path to train split 1 file list (ucfTrainTestlist/trainlist01.txt)>
```
2. Train on the converted bins:
```
python train.py --data_dir=<path to converted bins> --save_dir=<path to save checkpoints/summaries>
```

Summary graphs can be viewed using TensorBoard, using the following command in Terminal:
```
tensorboard --logdir=<path to saved checkpoints/summaries>
```

## Testing
In order to test our model on UCF-101 (split 1) on a single GPU:

1. Convert the test motion vector bins by running:
```
python convert_UCF_bin.py --data_dir=<path to motion vector data dir --out_dir=<path to output dir> --path_to_file_list=<path to test split 1 file list (ucfTrainTestlist/testlist01.txt)>
```
2. Test on the converted bins:
```
python test.py --data_dir=<path to converted bins> --save_dir=<path to saved checkpoints/summaries>
```



