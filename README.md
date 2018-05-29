fauxrec
=======

fauxrec (for **f**fmpeg + **a**ucat **x**11 **rec**order) is a shell script to
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

Requirements
------------

* ffmpeg (install from ports with `doas pkg_add ffmpeg`)
* OpenBSD's aucat(1)

Limitations
-----------

* No warning when it runs out of storage while streaming.
* Higher recording resolutions, non-default video codecs, or simultaneously
  running applications may affect performance while recording to the point of
  dropping frames and leading to desynchronization.
* The ffmpeg port's aac encoder fails when attempting to encode aac with
  constant bitrate. Workarounds are using aac with variable bitrate (thereby
  not following [YouTube recommended upload encoding
  settings](https://support.google.com/youtube/answer/1722171?hl=en)), or
  [building ffmpeg with libfdk_aac
  encoder](https://github.com/rfht/mystuff/tree/master/graphics/ffmpeg)
  and using that one.

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

`fauxrec -r 640x480 -a snd/0.mon screencast.mp4`
