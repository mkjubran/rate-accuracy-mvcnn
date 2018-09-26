# main
AVC/H.264 cropped Bitstream:
To produce cropped Bitstream use
./JM_Cropped_MV_Stats -svid {source video}

Outputs:
1- Cropped JM bitstream
2- MV.bin file where MV values are mapped to a grid of MBs
3- States (including rate) per frame for the cropped Bitstream
4- States (including rate) per frame for the Bitstream produced by the Original JM encoder

Options:
Various encoding parameters can be set directly such as


To use this .....
1-Edit the header of the MultiProcessGenerateJMMVComputeSaveOrigin shell script to specify the video coding parameters of the JM codec.
2-Edit the header of the  GenerateAndComputeSizeRatio-UCF-Split-1 shell script to specify the path of folder that includes all original UCF videos.
3-Run the MultiProcessGenerateJMMVComputeSaveOrigin script, this will produce three output files per input video stored in parent directly called JMMV. These files are
   a.MV.bins: contain the MV pairs for each encoding block, 
   b.Orig Stats file: text file that includes statics and information of encoding the video using the standard JM codec
   c.No Texture Stats file: text file that includes statics and information of producing the cropped bitstream
4-Edit the header and then run the CollectStatsPerVideo_UCF script. This will collect the statistics and rate information from “Orig Stats file” and “No Texture Stats file” of al videos and write it in one Stats_Orig_NOTexture output file.
5-Samples of these files for UCF-101 Test split is included in the Multi-CNN/DataFiles folder.
6-Get the correct decision vectors from the CNN model
7-To load the stats and decision vectors per video run the MainCreateProcessVideoObjects.m file. This will create separate object for each video with attributed based on the stats  and decision vectors files.
8-Edit the header and then run the AnalyticalMultiCNN_shortGamma_Rmotion.m to get the Multi-CNN threshold values (R_L and R_H). You will also get expected classification accuracy and required rate of Multi-CNN based on these threshold values.

