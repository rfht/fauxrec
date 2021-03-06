fauxrec
=======

fauxrec (for **f**fmpeg + **au**cat **x**11 **rec**order) is a shell script to
simultaneously record a desktop video stream and one or more audio streams. The
main goal is to allow screencasting with additional voice-over stream for
comments, to record instructions, demonstrations, Let's Plays... The license is
ISC (see [LICENSE.txt](LICENSE.txt)).

It is **not** meant for live streaming at this point, as it needs an additional
multiplexing step after finishing the recording.

Its defaults are set to create a .mp4 containered file, ready to upload e.g. to
YouTube. The encoding settings generally attempt to follow the the [YouTube
recommended upload encoding
settings](https://support.google.com/youtube/answer/1722171?hl=en).

Rationale
---------

To my knowledge, there is no dedicated screen recording solution available on
OpenBSD. The port of ffmpeg can fulfill these functions, but seems to be
hampered by apparent lack of or suboptimal multithreading support, resulting
in dropped frames when recording 1 video and 2 audio streams (monitoring stream
and microphone stream), as is needed for recording e.g. gameplay while
commenting.

Recording both video and audio with *just* ffmpeg on OpenBSD tends to be too
slow for high resolutions and adequate quality of the recorded streams.

It turns out that the reason for this is apparently lack of (or too little)
parallelism on th side of ffmpeg.

This script circumvents this by running multiple parallel recording streams
with both ffmpeg(1) and OpenBSD's aucat(1) and combining them after the
recording is complete.

Note that this is likely only useful on multiprocess systems/kernels.

Requirements
------------

* ffmpeg (install from ports with `doas pkg_add ffmpeg`)
* aucat(1) (present on OpenBSD, FreeBSD)

Limitations
-----------

* No warning when it runs out of storage while streaming.
* Higher recording resolutions, non-default video codecs, or simultaneously
  running applications may affect performance while recording to the point of
  dropping frames and leading to desynchronization. My testing was done on a
  dual-core i3-7100 with Hyperthreading, recording a 1920x1080 video stream,
  along with 2 audio streams (monitoring stream and microphone stream) without
  apparent performance issues.
* At this time, the muxing step hardcodes a stereo channel layout.
* The recording with ffmpeg's x11grab doesn't register when an application goes
  fullscreen, and it continues to record it as being run in a window.
* Currently requires a second audio stream, e.g. the monitoring stream (see
  below)
* Likely significantly worse performance on single-core CPUs/single-process
  systems.

Setting up the Monitoring Stream
--------------------------------

Refer to [FAQ 13](https://www.openbsd.org/faq/faq13.html#recordmon).

Usage
-----

```
-r <recording video resolution>
-a <additional audio stream>
-c <video codec>
-p <path>
-v1 <volume adjustment of stream 1>
-v2 <volume adjustment of stream 2>
```

Example(s):
-----------

`sh fauxrec -r 1920x1080 -a snd/0.mon screencast.mp4`

**Note: To stop the recording, press Ctrl-C (not [q]!). Then the script will
continue to combine (mux) the recorded streams.**
