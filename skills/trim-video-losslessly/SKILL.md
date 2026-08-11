---
name: trim-video-losslessly
description: Cut or trim a section out of a video or audio file without re-encoding. Use whenever the user wants to cut, trim, clip, shorten, or extract a time range from a media file — "first 30 seconds", "from 1:10 to 2:00", "cut the intro". Covers checking the file first and copying streams so the operation takes seconds, not minutes.
agents: media-ops
---

# Trim with -c copy — seconds, not minutes

Look before you touch:
`ffprobe -v error -show_format -show_streams uploads/clip.mp4` tells you the
container, codecs and duration, so you cut with the right arguments.

- Lossless cut by copying streams:
  `ffmpeg -ss 00:01:10 -to 00:02:00 -i uploads/clip.mp4 -c copy clip-cut.mp4`.
  Putting `-ss` before `-i` seeks fast; `-c copy` skips re-encoding.
- `-c copy` cuts on the nearest keyframe, so the start may shift by a second or
  two. When the cut must be frame-exact, drop `-c copy` and re-encode that one
  section — slower, but exact.
- **Never overwrite the source.** Write beside it with a suffix (`clip-cut.mp4`)
  and name the output path. Add `-y` only when overwriting is deliberately meant.
