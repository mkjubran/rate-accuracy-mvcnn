# Rate-Accuracy Trade-Off In Video Classification With Deep Convolutional Neural Networks

## Introduction

This repository contains our public tool for producing cropped AVC and HEVC bitstream, extracting optical flow approximation (MVs), and two classification architectures (2D-CNN and 3D-CNN).

## Prerequisites

In order to run thie code you will need:
1. Python 2.7 
2. ffmpeg (tested with version 2.8.15)

## Cropped AVC/H.264 Bitstream and Optical Flow Approximation (MVs)
To produce AVC/H.264 cropped bitstream and the approximated flow run
```
./JM_Cropped_MV_Stats -svid <input_video>
```

The JM_Cropped_MV_Stats script takes an avi input video to produce the cropped bitsream and flow estimates according to the following steps:
1. use ffmpeg to transcode the input video to YUV format with size WxH
2. encoded YUV video using the modified AVC/H.264 JM refernce software to produced the cropped x264 bitsream
3. decoded the cropped bitstream using the modified AVC/H.264 JM reference software to extract the MVs
4. map these MVs to a Gird with a dimension set according to the resolution of the input video **WxH** and the **res** parameter

Various encoding parameters can be set directly such as

Option | Description [default]
---|---
--svid |  Input video [./v_BoxingPunchingBag_g05_c01.avi]
--W  | width of the YUV video [320]
--H | height of thhe YUV video [240]
--ecfg |  JM configuration file [encoder_options.cfg]
--qp  |   QP value for P and I frames [40]
--sr  |   search range [16]
--res  |  resolution of the MB Grid (4,8,16) [8]

The JM_Cropped_MV_Stats generate the following items in JMMV parent directory:
- Cropped JM bitstream (x264)
- flow estimate file MV.bin
- States (including rate) per frame for the cropped bitstream
- Summary of states for the cropped bitstream
- States (including rate) per frame for the bitstream produced by the original JM encoder
- Summary of states for the original JM bitstream

Sample Output using default parameters:
%########################################################################################
JM config file=encoder_option2.cfg, Source Video=v_BoxingPunchingBag_g05_c01.avi, QP=40, Search Range=16, MV Resolution=8
%########################################################################################
- Converting source video to YUV format
- Producing cropped H.264 bitstream (encoder)
- Extracting MVs from the cropped H.264 bitstream (decoder)
- Original JM: encoding to produce rate (bps) per frame (optional: for comparison purposes) 
- Mapping MV to a grid according to Macroblocks positions
- Moving outputs to JMMV parent directory

## Cropped HEVC Bitstream and Optical Flow Approximation (MVs)
To produce HEVC cropped bitstream and the approximated flow run
```
./HM_Cropped_MV_Stats -svid <input_video>
```

The HM_Cropped_MV_Stats script takes an avi input video to produce the cropped bitsream and flow estimates according to the following steps:
1. use ffmpeg to transcode the input video to YUV format with size WxH
2. encoded YUV video using the modified HEVC refernce software to produced the cropped .bin bitsream
3. decoded the cropped bitstream using the modified HEVC reference software to extract the MVs
4. map these MVs to a Gird with a dimension set according to the resolution of the input video **WxH** and **res** parameter

Various encoding parameters can be set directly such as

Option | Description [default]
---|---
--svid |  input video [./v_BoxingPunchingBag_g05_c01.avi]
--W  | width of the YUV video [320]
--H | height of thhe YUV video [240]
--ecfg |  HM configuration file [encoder_rate_accuracy.cfg]
--qp  |   QP value for P and I frames [40]
--mcu |   maximum CTU size [16]
--mpd |   maximum partition depth [2]
--sr  |   search range [16]
--res  |  resolution of the CU Grid (4,8,16) [8]

The HM_Cropped_MV_Stats generate the following items in HMMV parent directory:
- Cropped HM bitstream (xbin)
- flow estimate file MV.bin
- Original HM: encoding and then decoding to produce rates and stats (optional: for comparison purposes)
- Mapping MV to a grid according to CU positions
- Moving outputs to HMMV parent directory

Sample Output using default parameters:
%########################################################################################
JM config file=encoder_rate_accuracy.cfg, Source Video=v_BoxingPunchingBag_g05_c01.avi, QP=40, Search Range=16, MV Resolution=8
%########################################################################################
- Converting source video to YUV format
- Producing cropped HEVC bitstream (encoder)
- Extracting MVs from the cropped HEVC bitstream (decoder)
- Original HM: encoding to produce rate (bps) per frame (optional) 
- Mapping MV to a grid according to Macr
- Moving outputs to HMMV parent directory


