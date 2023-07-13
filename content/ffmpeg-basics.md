+++
title = "ffmpeg - Transcoding Basics"
date = 2023-06-22
updated = 2023-06-22
authors = ["Calvin Yong"]

[taxonomies]
tags = ["cli", "ffmpeg"]
+++

This post on ffmpeg will focus on one of the basic use cases of ffmpeg: transcoding videos. The commands given in this post are the minimum command line arguments needed to transcode a video. For controlling encoding equality and encoding time, see my post on [constant quality and presets](@/ffmpeg-quality-presets.md)

<!-- more -->

Before showing how to remux or reencode a video, it helps to know the difference between containers and codecs. A video file has two parts: the container that holds the video and audio data, and the codecs that encode the video and audio data. For a video explaining video containers and codecs, I recommend [this video from ExplainingComputers](https://www.youtube.com/watch?v=-4NXxY4maYc). The basic idea to understand is that a collection of mp4 files can use different codecs between them, which some media players might not be able to play. For example, some iPhone apps might not be able to play Opus encoded audio from a mp4 file.

## Stream copy, or converting between video formats without reencoding

Some media players do not support playing certain containers like mkv, but the codecs in the video file are supported. In that case, we can simply convert from one container to another without transcoding.

```sh
ffmpeg -i in.mkv -c copy out.mp4
```

The `-c copy` argument tells ffmpeg to use the copy codec on both the video and the audio for the output file. The copy codec simply copies the video and audio data from the input video to the output video, and is very fast since there is no reencoding done.

If you have a file with multiple streams of the same type, like a video with Japanese and English dubs, then the above command will not choose all of the streams. See the ffmpeg documentation on [stream selection](https://ffmpeg.org/ffmpeg.html#Stream-selection)

Do note however that containers may only support certain video and audio codecs. For example, the webm and opus containers only support vorbis and opus, not AAC. See [this ffmpeg wiki page](https://trac.ffmpeg.org/wiki/Encode/HighQualityAudio#Containerformats) for a table of supported audio codecs for each container.

To find out what video and audio codecs your media file uses, you can use `mediainfo` command.

```sh
mediainfo video.mp4
```

Some video players also allow you to see the information on a video file. For example, while a video is playing on `mpv`, you can press the `i` key to show the information on a video.

See also the "Stream copy" section in the ffmpeg man page.

## Reencoding videos

Some players might support a container, but there might not be video/audio during playback. In that case, the video player might not support the codec for the video or audio. In those cases, we can use ffmpeg to reencode the video file to a supported format. The following example converts a video to an mp4 video with the h264 video codec and the aac audio codec.

```sh
ffmpeg -i in.mkv -vcodec libx264 -acodec aac out.mp4
```

We can also reencode a video to use a more efficient codec. That is, a codec that yields a smaller representation of the file but at a similar quality, or yields a better quality at a similar file size. For example, the following example encodes a video using the HEVC video codec and the Opus codec.

```sh
ffmpeg -i in.mkv -vcodec libx265 -acodec libopus out.mp4
```

Do keep in mind that typically efficient codecs take more computing time. Also, it may not be worth reencoding a video if you get a video with a larger file size compared to the input video. Reencoding audio is also not always worth it since audio streams are already pretty small compared to the video stream.
