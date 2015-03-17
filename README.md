# video-exports
Apple automator export script for mp4 to lower res mp4. Also to ogv, webm and one still frame jpg for a cover picture.



```
#Compile ffmpeg into /usr/local/bin/ffmpeg; compilation required to include libfdk-aac for mp4 audio
#converts from mp4 to other web formats; can likely comment out mp4 encoding if existing file is already a good size
#Using: ffmpeg version N-70742-gd79f7bf-tessus Copyright (c) 2000-2015 the FFmpeg developers built with Apple LLVM version 6.0 (clang-600.0.57) (based on LLVM 3.5svn) configuration: --prefix=/usr/local --as=yasm --extra-version=tessus --disable-shared --enable-static --disable-ffplay --enable-gpl --enable-version3 --enable-nonfree --enable-libfdk-aac --enable-libfreetype --enable-pthreads --enable-postproc --enable-libmp3lame --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libx265 --enable-libxvid --enable-libspeex --enable-bzlib --enable-zlib --enable-libsoxr --enable-libwavpack --enable-libvo-aacenc --enable-libvpx --enable-libgsm --enable-libopus --enable-libmodplug --enable-fontconfig --enable-libfreetype --enable-libass --enable-libbluray --enable-filters --disable-indev=qtkit --disable-indev=x11grab_xcb --enable-runtime-cpudetect


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
-c:v libx264 -preset slow -profile:v high -level 4.1 -c:a libfdk_aac -b:a 128k "$newpath.mp4" \
-vcodec libvpx -b:v 1500k -acodec libvorbis -ab 160000 -f webm -g 30 "$newpath.webm" \
-c:v libtheora -q:v 10 -c:a libvorbis -q:a 10 "$newpath.ogv" \
-ss 00:00:02 -q:v 0 -vframes 1 -y "$newpath.jpg"
done
```

https://github.com/geranyl/video-exports/tree/master/html5VideoExport_all.workflow/Contents/QuickLook
![Workflow screenshot](html5VideoExport_all.workflow/Contents/QuickLook/Preview.png?raw=true "Automator Workflow")