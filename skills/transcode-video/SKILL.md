---
name: transcode-video
description: Convert a video or audio file to another format or codec, compress it, or pull audio or a frame out of it. Use whenever the user wants to convert, re-encode, compress, shrink, or change the format/codec of a media file, extract its audio, or grab a thumbnail or frame. For a pure cut with no format change, use trim-video-losslessly.
agents: media-ops
---

# Transcode: pick the codec for the job

Run `ffprobe -v error -show_format -show_streams INPUT` first — the current codecs
decide what is worth changing. This build has x264, x265, VP9, AV1 (libaom and
SVT), Opus, MP3, Vorbis and AAC.

- H.264, widest compatibility:
  `ffmpeg -i uploads/clip.mov -c:v libx264 -crf 20 -c:a aac clip.mp4`. Lower
  `-crf` is higher quality and bigger; 18–23 is a good range.
- Smaller at the same quality: `-c:v libx265 -crf 26` (H.265) or
  `-c:v libsvtav1 -crf 30` (AV1) — slower encodes, and not every player supports
  them.
- Extract audio only:
  `ffmpeg -i uploads/clip.mp4 -vn -c:a libmp3lame -q:a 2 clip.mp3`.
- Grab a single frame:
  `ffmpeg -ss 00:00:05 -i uploads/clip.mp4 -frames:v 1 frame.jpg`.
- Write beside the input with a suffix; never overwrite the source.
