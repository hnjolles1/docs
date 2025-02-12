---
title: Benchmark Transcoding
---

This guide provides steps to test the performance of your GPU(s) with the `livepeer_bench` benchmark transcoding tool provided with the [Livepeer install](/installation/install-livepeer/).

The benchmarking tool is designed to: 

- Simulate live-mode to measure transcoding as if connected to the network
- Gauge local transcoding capacity
- Provide output statistics to show real-time segment ratio and stream duration ratio
- Provide for adjusting configurations according to GPU capacity  

## Transcoding Performance

Overall transcoding performance on the network depends on the speed video is transcoded and uploaded/downloaded from an orchestrator.

The transcode time should be as low as possible. At the least, it should be lower than the total [segment duration](/video-miners/terminology#stream-duration-ratio), which is faster than real-time. 

If you want to get an approximation of how many streams you can transcode simultaneously, increase the number of concurrent sessions with the `-concurrentSessions` flag to assess the total transcoding time. 

> **Note:** After running your benchmark transcoding, you can follow up with further instructions about setting [session limits](/video-miners/guides/session-limits).

## Start Benchmarking

1. Download the test stream we provide for benchmarking transcoding:

```bash
wget -c https://storage.googleapis.com/lp_testharness_assets/bbb_1080p_30fps_1min_2sec_hls.tar.gz
tar -xvf bbb_1080p_30fps_1min_2sec_hls.tar.gz
ls bbb/   # Should print the stream *.ts segments and source.m3u8 manifest
```
> **Note:** It is important to note that running this benchmarking only gauges local transcoding capacity.

2. Download the [common output rendition configuration](https://github.com/livepeer/go-livepeer/blob/master/cmd/livepeer_bench/transcodingOptions.json) `.json` file that you can copy and save in the Livepeer folder as `transcodingOptions.json`.

1. Run `livepeer_bench`

The number and type of [output renditions](/video-miners/terminology#transcoding) will impact benchmark results. 


```bash
livepeer_bench \
    -in bbb/source.m3u8 \
    -transcodingOptions transcodingOptions.json \
    -nvidia <NVIDIA_GPU_IDs> # Only required for transcoding with GPUs
    -concurrentSessions <CONCURRENT_SESSIONS>
```

### Flags

- The `-nvidia` flag is only required when transcoding with Nvidia GPUs. It is used to specify a comma-delimited string of Nvidia GPU IDs.

- The `-concurrentSessions` flag is used to specify the number of concurrent transcoding sessions. The default value is 1.

### Livestreams

Livestreams are the most common type of workload on the network. By default, the flag is set to live mode `-live=*true*` so that the segments of the stream will be queued one-by-one as they arrive in a livestream.

If you want to queue all segments at once without any gaps in time, thus replicating a video-on-demand scenario, you can switch off live mode by changing the 'live' flag to *`=false`*.

**For example:**

```bash
livepeer_bench \
  -in bbb/source.m3u8 \
  -live=false
```
> **Note:** for the purposes of this example above, all other flags are omitted.

The first few lines of the output should display the source file, the output rendition profiles, and the number of concurrent sessions.

**For example:**

```bash
*---------------------*----------------------------------------------*
| Source File         | .../go-livepeer/bbb/source.m3u8              |
| Transcoding Options | P240p30fps16x9,P360p30fps16x9,P720p30fps16x9 |
| Concurrent Sessions | 1                                            |
| Live Mode           | true                                         |
*---------------------*----------------------------------------------*
```

The transcoding metrics will be output in CSV format per segment as each segment gets transcoded.

**For example:**

```bash
timestamp,session,segment,seg_dur,transcode_time
2021-03-12 00:21:29.1412,0,0,2,0.2545
2021-03-12 00:21:30.998,0,1,2,0.1107
2021-03-12 00:21:32.9816,0,2,2,0.09381
2021-03-12 00:21:34.9786,0,3,2,0.09031
2021-03-12 00:21:36.9806,0,4,2,0.09178
2021-03-12 00:21:38.9811,0,5,2,0.09216
2021-03-12 00:21:40.9831,0,6,2,0.09363
2021-03-12 00:21:42.9874,0,7,2,0.09746
2021-03-12 00:21:44.9885,0,8,2,0.09811
2021-03-12 00:21:46.9851,0,9,2,0.09412
```

When all the transcoding sessions end, metrics output will be generated. 

- `Real-Time Segs Ratio` captures the number of segments transcoded in real-time. 

- `Real-Time Duration Ratio` captures the total time taken to transcode all segments relative to the total duration of all source segments.

```bash
*------------------------------*---------------------*
| Concurrent Sessions          | 1                   |
| Total Segs Transcoded        | 10                  |
| Real-Time Segs Transcoded    | 10                  |
| * Real-Time Segs Ratio *     | 1                   |
| Total Source Duration        | 20s                 |
| Total Transcoding Duration   | 1.1165546499999999s |
| * Real-Time Duration Ratio * | 0.05583             |
*------------------------------*---------------------*
```

You can export the per-segment CSV data to an `output.csv` file to analyze it with other tools:

```bash
livepeer_bench \
    -in bbb/source.m3u8 \
    -transcodingOptions transcodingOptions.json \
    -nvidia <NVIDIA_GPU_IDs> # Only required for transcoding with GPUs
    -concurrentSessions <CONCURRENT_SESSIONS> > output.csv
```

