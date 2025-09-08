# Event Camera Simulator

Adaptation of v2e framework from

Y. Hu et al. 2021. v2e: From video frames to realistic DVS events. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR). IEEE, 1312–1321.

SuperSlomo model checkpoint for RGB frame interpolation can be found in [SuperSloMo39.ckpt](https://drive.google.com/file/d/1ETID_4xqLpRBrRo1aOT7Yphs3QqWR_fx/view?usp=sharing)


### Usage
First run `slomo_generator.py` for frame interpolation. 

```
slomo_generator.py [-h] [--model MODEL] [--slowdown_factor SLOWDOWN_FACTOR] [--fps FPS] [--start_time START_TIME]
                          [--end_time END_TIME] [--batch_size BATCH_SIZE]
                          input_dir output_dir mp4_output_path
```

```
positional arguments:
  input_dir             Path to input directory with PNGs and timestamps.csv
  output_dir            Path to output directory for interpolated PNGs and timestamps.csv
  mp4_output_path       Path to output demo mp4 video file

options:
  -h, --help            show this help message and exit
  --model MODEL         Path to SuperSloMo model checkpoint
  --slowdown_factor SLOWDOWN_FACTOR
                        Slowdown factor (default: 10)
  --fps FPS             Output video frame rate (default: 30)
  --start_time START_TIME
                        Start time offset in seconds from first frame's timestamp
  --end_time END_TIME   End time offset in seconds from first frame's timestamp
  --batch_size BATCH_SIZE
                        Batch size for processing (default: 128)
```

Then run `event_generator_pqdm.py` for fast parallel event generation. `event_generator.py` is for slower single thread event generation. 

```
usage: event_generator_pqdm.py [-h] [--group_size GROUP_SIZE] [--workers WORKERS] [--mp4_output MP4_OUTPUT] [--out_fps OUT_FPS]
                               [--in_slowdown IN_SLOWDOWN] [--threshold_pos THRESHOLD_POS] [--threshold_neg THRESHOLD_NEG]
                               [--contrast CONTRAST] [--cutoff_hz CUTOFF_HZ] [--intensity_dependent] [--disable_erc] [--disable_stc]
                               [--disable_afk] [--fast_mode] [--erc_max_rate ERC_MAX_RATE] [--erc_window_size ERC_WINDOW_SIZE]
                               [--stc_mode {STC_CUT_TRAIL,STC_KEEP_TRAIL,TRAIL}] [--stc_threshold_us STC_THRESHOLD_US]
                               [--afk_patch AFK_PATCH] [--afk_low_freq AFK_LOW_FREQ] [--afk_high_freq AFK_HIGH_FREQ]
                               [--afk_diff_thresh_s AFK_DIFF_THRESH_S]
                               input_dir output_dir
```

```
Parallel DVS Event Generator with PQDM (based on v2e)

positional arguments:
  input_dir             Input directory with PNG frames and timestamps.csv
  output_dir            Output directory for event PNG frames and timestamps.csv

options:
  -h, --help            show this help message and exit

Parallel Processing Options:
  --group_size GROUP_SIZE
                        Number of frames per processing group (default: 1000)
  --workers WORKERS     Number of parallel workers (default: 8)

Output Options:
  --mp4_output MP4_OUTPUT
                        Optional MP4 output video file for visualization

Core Processing Parameters:
  --out_fps OUT_FPS     Output video frame rate (default: 30)
  --in_slowdown IN_SLOWDOWN
                        Input frame grouping factor (default: 10.0)
  --threshold_pos THRESHOLD_POS
                        Positive event threshold (default: 0.25)
  --threshold_neg THRESHOLD_NEG
                        Negative event threshold (default: 0.25)
  --contrast CONTRAST   Event visualization contrast (default: 16)

IIR Low Pass Filter Parameters:
  --cutoff_hz CUTOFF_HZ
                        IIR low pass filter cutoff frequency (0 = disabled)
  --intensity_dependent
                        Use intensity-dependent time constants

Filter Control:
  --disable_erc         Disable Event Rate Controller
  --disable_stc         Disable Spatio-Temporal Correlation Filter
  --disable_afk         Disable Anti-Flicker Filter
  --fast_mode           Disable all filters for maximum speed

Event Rate Controller (ERC) Parameters:
  --erc_max_rate ERC_MAX_RATE
                        Maximum allowed event rate (default: 1,000,000)
  --erc_window_size ERC_WINDOW_SIZE
                        ERC time window size in seconds (default: 0.01)

Spatio-Temporal Correlation (STC) Parameters:
  --stc_mode {STC_CUT_TRAIL,STC_KEEP_TRAIL,TRAIL}
                        STC filter mode (default: STC_CUT_TRAIL)
  --stc_threshold_us STC_THRESHOLD_US
                        STC burst detection threshold in microseconds (default: 1000)

Anti-Flicker (AFK) Parameters:
  --afk_patch AFK_PATCH
                        AFK spatial patch size (default: 4)
  --afk_low_freq AFK_LOW_FREQ
                        AFK lower bound of flicker band (default: 49.0)
  --afk_high_freq AFK_HIGH_FREQ
                        AFK upper bound of flicker band (default: 51.0)
  --afk_diff_thresh_s AFK_DIFF_THRESH_S
                        AFK period variation threshold (default: 0.002)

Examples:
  Basic usage with parallel processing:
    python event_generator_pqdm.py input_dir output_dir --group_size 1000 --workers 8
    
  High sensitivity with custom slowdown:
    python event_generator_pqdm.py input_dir output_dir --threshold_pos 0.1 --threshold_neg 0.1 --in_slowdown 5
    
  Fast mode (no filters) with parallel processing:
    python event_generator_pqdm.py input_dir output_dir --fast_mode --workers 12
    
  With MP4 video output:
    python event_generator_pqdm.py input_dir output_dir --mp4_output output.mp4 --group_size 500
```
