---
title: high-bandwidth 3D image compression to boost predictive life sciences
author: Peter Steinbach, Jeffrey Kelling (presenter)
origin: Scionics Computer Innovation GmbH, Helmholz-Zentrum Dresden Rossendorf
email: steinbach@scionics.de
date: May 11, 2017
---


# Before I start

## Jeffrey != Peter 

[columns,class="row vertical-align"]

[column,class="col-xs-6"]

<center>

*presenter*

![Jeffrey Kelling (HZDR)](img/jeffrey_kelling.png){ width=50% }  

</center>

[/column]

[column,class="col-xs-6"]

<center>

*author*

![Peter Steinbach (Scionics)](img/peter_steinbach.jpg){ width=50% }  

</center>

[/column]

[/columns]

## Scionics Who?

[columns,class="row vertical-align"]

[column,class="col-xs-6"]

![](img/scionics_main_logo.png)  
[Scionics Computer Innovation GmbH](https://www.scionics.com/)

[/column]

[column,class="col-xs-6"]

- founded in 2000, Dresden (Germany)
- service provider to the [Max Planck Institute of Molecular Cell Biology and Genetics](https://www.mpi-cbg.de/home/)  

    - scientific computing facility
    - IT infrastructure
    - public relations

- member of the [GPU Center of Excellence](gcoe-dresden.de) (community of industrial and academic developers/scientists using GPUs)

[/column]

[/columns]

[notes]
- presentation of our institute
[/notes]

## This Talk is


[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>
![](img/opensource-550x475.png)  

**[github.com/psteinb/gtc2017](https://github.com/psteinb/gtc2017)**
</center>

[/column]

. . . 

[column,class="col-xs-4"]

- code snippets

- presentation links

- [open an issue](https://github.com/psteinb/gtc2017/issues) for questions

[/column]

[/columns]


## Outline

<div style="font-size : 1.5em">

<center>
1. Scientific Motivation

2. Sqeazy library

3. Results
</center>

</div>


# Big Data Deluge in Systems Biology

## [SPIM](https://en.wikipedia.org/wiki/Light_sheet_fluorescence_microscopy)

<center>

![Selective Plane Illumination Microscopy](img/1280px-Spim_prinziple_en.gray.png)

</center>


## Biologists love this!

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

<video width="1200" poster="movies/Drosophila_Embryogenesis_beads_removed.png" controls>
<source src="movies/Drosophila_Embryogenesis_beads_removed.webm" type='video/webm; codecs="vp8.0, vorbis"'>
<source src="movies/Drosophila_Embryogenesis_beads_removed.mp4" type='video/mp4'>
<p>Movie does not work! Sorry!</p>
</video>

</center>

[/column]

[column,class="col-xs-4"]

3D rendering of Drosophila embryogenesis time-lapse data reconstructed from 5 angles SPIM recording  

[credits to Pavel Tomancak (MPI CBG)](https://extweb-srv5.mpi-cbg.de/de/research/research-groups/pavel-tomancak/movies.html)

[/column]

[/columns]

## But ... 

[columns,class="row vertical-align"]

[column,class="col-xs-6"]

<center>

![Design Draft of a modern SPIM microscope, credits Nicola Maghelli (MPI CBG, Myers lab)](img/xscope_schematic.png){width=100%}  

</center>

[/column]


[column,class="col-xs-6"]

<center>

- *today*:

	+ each CMOS camera can record 850 MB/s of 16bit grayscale pixels
	+ 2 cameras per scope, 1.7 GB/s

- scientists would like to capture long timelapses *1-2 days* (or more)

- total data volume per 1-2 day capture:  

*150-300 TiB* raw volume

= *57 - 114 kEUR* in SSDs

</center>

[/column]

[/columns]


## IT to the rescue

<center>

![](fig/spim_dataflow_and_infrastructure.svg){ width=85% }  

</center>

## <span style="color: white; background-color: #002b36;">Does that scale?</span> {data-background="img/ieee_data_deluge.jpg"}


# [Sqeazy](https://github.com/sqeazy/sqeazy)

## [Open-source Compression Library](https://github.com/sqeazy/sqeazy){ target="_blank" }

<center>

<a href="https://github.com/sqeazy/sqeazy" target="_blank">
![](img/sqeazy-on-github.png){ width=90% }
</a>

</center>

## Yet another compression library?

[columns,class="row vertical-align"]

[column,class="col-xs-6"]

<center>

![[wikimedia commons](https://commons.wikimedia.org/wiki/File:Paris_Tuileries_Garden_Facepalm_statue.jpg)](img/800px-Paris_Tuileries_Garden_Facepalm_statue.jpg)

</center>

[/column]


[column,class="col-xs-6"]

- heart of sqeazy: pipeline mechanism
    - transform data so that it can be compressed best
    - use very good and fast encoders as end of the pipeline, e.g. [zstd](https://github.com/facebook/zstd), [lz4](https://github.com/lz4/lz4), [blosc](http://www.blosc.org/), ...  
    *use them, don't reinvent them!*


- do it fast! (multi-core, SIMD)

- written in C++11 (soon C++14)

[/column]

[/columns]

## Can we do better?

<center>

3D in space = 2D in space + time!

</center>

. . .  

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![[wikimedia commons](https://commons.wikimedia.org/wiki/File:Resolution_of_SD,_Full_HD,_4K_Ultra_HD_%26_8K_Ultra_HD.svg)](img/800px-Resolution_of_SD,_Full_HD,_4K_Ultra_HD_&_8K_Ultra_HD.svg.png){ width=80% }

</center>

[/column]


[column,class="col-xs-4"]

- multimedia industry and video codec research has worked in high-bandwidth/low-latency regime for years
- reuse their expertise through free available codec libraries
- currently looking into [h264/MPEG-4 AVC](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) and [h265/hevc](https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding), others are possible

[/column]

[/columns]


## Challenge: SPIM data

<center>

![](plots/corpus_drange.svg){width=90%}  

</center>

[columns,class="row"]

[column,class="col-xs-6"]

- raw data is encoded as _grey16_

[/column]


[column,class="col-xs-6"]

- pixel intensities occupy more than 8-bits  
mean +/- std = 11 +/- 3

[/column]

[/columns]


## Solution: Quantize data



[columns,class="row vertical-align"]

[column,class="col-xs-8"]


<center>

![](data/sqy/sqy_median_based_mnmse.svg){width=100%}

</center>

[/column]

[column,class="col-xs-4"]

- lossy bucket based quantisation  
(16 -> 8 bits per pixel transformation)
- quality loss minimal
- 8-bit per channel encoding is the standard input for video codecs
- bandwidth enough to take 8 cameras

[/column]

[/columns]

## ffmpeg

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

- using [ffmpeg](https://ffmpeg.org/) framework to interface sqeazy to

    - support CPU and GPU based encoding/decoding

    - enable future directions to non-x86 platforms 
    
    - Linux, macOS, Windows supported

- steep learning curve for using libavcodec API

- for this talk: ffmpeg 3.0.7

[/column]

[column,class="col-xs-4"]

![](img/ffmpeg-logo.png){ width=80% }

[/column]

[/columns]

## hardware accelerated codecs

- our production environment: Windows (microscope) and Linux (HPC) based

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![from [ffmpeg wiki](https://trac.ffmpeg.org/wiki/HWAccelIntro#Platformsoverview)](img/ffmpeg_api_overview.png){width=100%}

</center>

[/column]

[column,class="col-xs-4"]

- rarely any single library supports hardware accelerated video encoding uniformly across platforms

- ffmpeg+nvenc meets our production requirements

- encapsulates external dependencies (easier comparison)

[/column]

[/columns]


# Results

## benchmark platform

[columns,class="row"]

[column,class="col-xs-6"]

<div style="font-size: 1.5em; text-align: center">
*hardware*
</div>

- dual socket Intel Xeon [E5-2680v3](http://www.cpu-world.com/CPUs/Xeon/Intel-Xeon%20E5-2680%20v3.html) (2x12c)
- 128GB DDR4 RAM
- 2x [Nvidia GeForce GTX1080](https://en.wikipedia.org/wiki/GeForce_10_series)
- CentOS 7.1
- host CPU limited to 8 threads (production environment)

[/column]


[column,class="col-xs-6"]

<div style="font-size: 1.5em; text-align: center">
*software*
</div>

- [ffmpeg](https://ffmpeg.org/) 3.0.7 ([build instructions](https://gist.github.com/Brainiarc7/988473b79fd5c8f0db54b92ebb47387a))
- [x264]( http://git.videolan.org/git/x264.git ) (commit 90a61ec764)
- [x265]( https://bitbucket.org/multicoreware/x265/wiki/Home ) 2.4
- [GNU gcc](https://gcc.gnu.org/) 6.3 (5.4 when CUDA is required)
- [Nvidia Media SDK](https://developer.nvidia.com/nvidia-video-codec-sdk) v7.1.9
- Nvidia driver 375.26
- CUDA 8.0.61
- [snakemake](https://snakemake.readthedocs.io/en/stable/) 3.11.2 to orchestrate benchmarks

[/column]

[/columns]

## what I measured

- simple workflow based on ffmpeg performed on all:  

  	1. quantize .tif images to YUV 4:2:0 with sqeazy (produce *input.y4m*)
  	2. encode *input.y4m* video with ffmpeg (take time, input/output files in ramdisk)
  	3. decode encoded.raw to obtain _roundtrip.y4m_
  	4. compare quality of *input.y4m* and _roundtrip.y4m_
  
  
<br>
  
  
- all timings based on _/usr/bin/time_ if not stated otherwise
- orchestration on our HPC infrastructure with [snakemake](https://snakemake.readthedocs.io/en/stable/)


## CPU only

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![](data/ffmpeg//ffmpeg_video_codec_cpuonly.svg){ width=90% }

</center>


[/column]

[column,class="col-xs-4"]

- x264 is fast, but doesn't provide high compression
- x265 is slow, but does provide high compression

- codec preset study ongoing with downstream analysis/processing

<center style="font-size: 1.25em">

**GPUs to the rescue?**

</center>


[/column]

[/columns]


## compare timings

```
$ time ffmpeg -i input.y4m -c:v nvenc_h264 -preset llhp -2pass 0 ...
$ nvprof --print-api-trace ffmpeg -i input.y4m -c:v nvenc_h264   ...
```

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![](data/ffmpeg/ffmpeg_timing_difference.svg){ width=90% }

</center>


[/column]

[column,class="col-xs-4"]

- nvprof api trace: time delta from cuCtxCreate/cuCtxDestroy 

- nvenc codec consumes 30-50% of the ffmpeg process time only

- ffmpeg induces quite some overhead on top of nvenc!

[/column]

## GPU enhanced encoding


[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![](data/ffmpeg/ffmpeg_cpugpu_video_codecs_enhanced.svg){ width=90% }

</center>


[/column]

[column,class="col-xs-4"]

- here:   
*cuCtxCreate/Destroy based timing*
- _nvenc_ offers improved compression ratios in comparison to _libx26{4,5}_ (preset definitions differ)
- nvenc bandwidths are surprisingly low

[/column]

## Profiling details

```
$ nvprof ffmpeg -i input.y4m -c:v nvenc_h264 -preset llhp -2pass 0 -gpu 1 -y output.h264
```

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![](img/nvprof_nvenc_h264.png){ width=100% }

</center>


[/column]

[column,class="col-xs-4"]

- to no surpise: *nvenc encoding is bound by host-device transfers* (90%)

<center>
**Can it still be that slow?**
</center>

[/column]

## GPU enhanced encoding (cont.)

[columns,class="row vertical-align"]

[column,class="col-xs-8"]

<center>

![](data/ffmpeg/ffmpeg_cpugpu_video_codecs_sdk.svg){ width=90% }

</center>


[/column]

[column,class="col-xs-4"]

- here:   
*timing from Nvidia Video SDK NvEncodeLowLatency*
- _nvenc_ superior to _libx26{4,5}_ 
- NvEncodeLowLatency timings:

    + exclude driver initialisation 
    + exclude memory initialisation 

[/column]

# Summary

## high-bandwidth 3D image compression

- tough business given modern CMOS cameras (around 1GB/s at 16bit greyscale)

- multi-core implementations very competitive   
  (either in compression ratio or speed)
  
     + many codecs available
     
     + manu configuration parameters
     
     + many bit ranges coming about (8,10,12 bits)
  

## GPUs for 3D image compression?

- nvenc through ffmpeg difficult to use/measure   
(memory traffic, implementation quality poor?)

- raw nvenc API suitable for high-bandwidth compression

    + NvEncodeLowLatency timings ignores driver and memory initialisation  
    (represents scenario of constant streaming/encoding)
    
    + nvenc API useful on the microscope only, i.e. in streaming mode  
    (at best if compression pipeline is on the device as well)

    + PCIe bus apparently a bottleneck
    

## Thank you!

[columns,class="row vertical-align"]

[column,class="col-xs-4"]

<center style="font-size: 1.25em">
For questions, concerns or suggestions:
  
[Open an issue, please!](https://github.com/psteinb/gtc2017/issues)

</center>

[/column]

[column,class="col-xs-8"]

<center>
![](img/opensource-550x475.png)  

**[github.com/psteinb/gtc2017](https://github.com/psteinb/gtc2017)**
</center>

[/column]


[/columns]
