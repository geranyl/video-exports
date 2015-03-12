# video-exports
Apple automator export script for mp4 to lower res mp4. Also to ogv, webm and one still frame jpg for a cover picture.

Download and install static package from https://www.ffmpeg.org/download.html into /usr/local/bin/ffmpeg
Converts from mp4 to other web formats; can likely comment out mp4 encoding if existing file is already a good size
Using: ffmpeg version 2.5.3-tessus Copyright (c) 2000-2015 the FFmpeg developers built on Jan 10 2015 01:19:50 with Apple LLVM version 6.0 (clang-600.0.56) (based on LLVM 3.5svn) configuration: --cc=/usr/bin/clang --prefix=/Users/tessus/data/ext/ffmpeg/sw --as=yasm --extra-version=tessus --disable-shared --enable-static --disable-ffplay --enable-gpl --enable-pthreads --enable-postproc --enable-libmp3lame --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libx265 --enable-libxvid --enable-libspeex --enable-bzlib --enable-zlib --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libxavs --enable-libsoxr --enable-libwavpack --enable-version3 --enable-libvo-aacenc --enable-libvo-amrwbenc --enable-libvpx --enable-libgsm --enable-libopus --enable-libmodplug --enable-fontconfig --enable-libfreetype --enable-libass --enable-libbluray --enable-filters --disable-indev=qtkit --disable-indev=x11grab_xcb --enable-runtime-cpudetect

```
for f in "$@"
do
	ext=${f##*.}
	basepath=`dirname "$f"`
	filename=`basename "$f"`
	rootname=`basename "$f" ".$ext"`
	newpath="$basepath/output/$rootname"
	mkdir -p "$basepath/output/"
	# -y to overwrite files without prompting
	# 2> outputs a log to the desktop in case of errors
	# jpg output slightly off start time, otherwise sometimes blank 
    /usr/local/bin/ffmpeg -i "$f" -y 2> "$HOME"/Desktop/ffmpeglogger.txt \
	-vcodec libx264 -preset slow -an -acodec libfaac -b:a 128k "$newpath.mp4" \
	-vcodec libvpx -b:v 1500k -acodec libvorbis -ab 160000 -f webm -g 30 "$newpath.webm" \
	-c:v libtheora -c:a libvorbis "$newpath.ogv" \
	-ss 00:00:02 -q:v 0 -vframes 1 -y "$newpath.jpg"
done
```