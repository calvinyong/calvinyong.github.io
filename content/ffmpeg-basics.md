+++
title = "(DRAFT) ffmpeg - transcoding basics"
date = 2023-06-08
updated = 2023-06-08

[taxonomies]
tags = ["cli", "ffmpeg"]
+++

This first post on ffmpeg focus on one of the basic use cases of ffmpeg: transcoding videos. Transcoding audio is also possible with ffmpeg, but the following examples will focus on videos.

<!-- more -->

Before showing how to remux or reencode a video, it helps to know the difference between containers and codecs. A video file has two parts: the container that holds the video and audio data, and the codecs that encode the video and audio data. For a video explaining video containers and codecs, I recommend [this video from ExplainingComputers](https://www.youtube.com/watch?v=-4NXxY4maYc). The main idea to understand is that a collection of mp4 files can use different codecs between them, which some media players might not be able to play.

## Stream copy, or converting between video formats without reencoding

Some media players do not support playing certain containers like mkv, but the codecs in the video file are supported. In that case, we can simply convert from one container to another without transcoding.

```sh
ffmpeg -i in.mkv -c copy out.mp4
```

The `-c copy` argument tells ffmpeg to use the copy codec on both the video and the audio for the output file. The copy codec simply copies the video and audio data from the input video to the output video.

Do note however that containers may only support certain video and audio codecs. For example, the webm and ogg formats only support vorbis and opus, not AAC. See [this ffmpeg wiki page](https://trac.ffmpeg.org/wiki/Encode/HighQualityAudio#Containerformats) for a table of supported audio codecs for each container.

See also the "Stream copy" section in the ffmpeg man page.

## Reencoding videos
