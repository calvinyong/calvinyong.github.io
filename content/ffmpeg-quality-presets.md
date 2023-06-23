+++
title = "ffmpeg - Constant Quality and Presets"
date = 2023-06-23
updated = 2023-06-23

[taxonomies]
tags = ["cli", "ffmpeg"]
+++

This post will show how to control the quality of your output file and the encoding time when using `libx264` or `libx265`. This will only give a summary of using constant quality / constant rate factor mode. For more information, see this ffmpeg wiki article on [H.264 Video Encoding](https://trac.ffmpeg.org/wiki/Encode/H.264)

## Constant Rate Factor (CRF)

If you only care about achieving the best compression rate at some target quality, and do not care about the file size, then constant quality / constant rate factor (CRF) mode is a great mode to use. CRF is controlled using the `-crf` option. The range for 8-bit `libx264` is 0-51, where lower is better quality, but a sensible range is 17–28, and the default value is 23. By default, `libx264` will use CRF mode.

```
ffmpeg -i in.mkv -vcodec libx264 -acodec copy out.mkv
```

Note that we added `-acodec copy` to the command. Without it, ffmpeg reencode the audio part using the default audio encoder. To change the default CRF value, use the following command:

```sh
ffmpeg -i in.mkv -vcodec libx264 -crf 23 -acodec copy out.mkv
```

<!-- more -->

## Presets

A preset is a collection of options for the encoder to adjust the compression ratio and encoding speed. While x264 does have 10 presets, I personally would only consider superfast, medium, and slow. Slow can be fine for `libx264`, but would probably be too slow for `libx265`. Superfast is useful for when you are streaming to a platnform or recording. To set a preset, use the `-preset` option.

```sh
ffmpeg -i in.mkv -vcodec libx264 -preset medium -acodec copy out.mkv
```

## Values that I Personally Use

From my experience and for my use case, I find using a CRF value of 20 for `libx264` and a preset value of slow to be a good balance of video quality, file size, and encoding time.

```sh
ffmpeg -i in.mkv -vcodec libx264 -crf 20 -preset slow -acodec copy out.mkv
```

For `libx265`, I would not change the default preset since `libx265` takes more time to encode a video compared to `libx264`.
