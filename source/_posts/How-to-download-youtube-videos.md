---
title: How to download youtube videos
date: 2020-09-05 21:25:03
tags:
---

## 项目

- [youtube-dl](https://github.com/ytdl-org/youtube-dl)
- [YoutubeDownloader](https://github.com/Tyrrrz/YoutubeDownloader)

## 源码

```python

import os

ROOT_DIR = os.getcwd()
OUTPUT_DIR = os.path.join(ROOT_DIR, 'output')
PLYLIST_URL = r'https://www.youtube.com/playlist?list=PLSW2D61TnopSpaPWy_JLcMu4Qjt7iUEwN'
DOWNLOAD_CMD = r'youtube-dl -f {} -o "' + ROOT_DIR +'/%(playlist_index)s - %(title)s.%(ext)s" {}'
EXTRA_OPTIONS = r''
# EXTRA_OPTIONS = r' --reject-title "Alphablocks \| Spelling Adventures! \| Learn to Read"'
# EXTRA_OPTIONS += r' --playlist-items 1-21,23-25'

def download_va():
    cmd = DOWNLOAD_CMD.format("248,140", PLYLIST_URL) + EXTRA_OPTIONS
    print(cmd)
    os.system(cmd)
    
def combine_audio_video(source_dir, output_dir, a_ext='.m4a', v_ext='.mp4', f_ext='.mp4'):
    for fil in os.listdir(source_dir):
        print(fil)
        if fil.endswith(a_ext):
            clip_name = fil.split('.')[0]
            print(clip_name)
            video_name = clip_name + v_ext
            final_name = clip_name + f_ext
            print(final_name)
            video_path = os.path.join(source_dir, video_name)
            audio_path = os.path.join(source_dir, fil)
            final_path = os.path.join(output_dir, final_name)
            cmd = 'ffmpeg -y -i "%s" -i "%s" -c copy "%s"' % (video_path, audio_path, final_path)
            os.system(cmd)
            
if __name__ == "__main__":
    if not os.path.exists(OUTPUT_DIR):
        os.mkdir(OUTPUT_DIR)
    download_va()
    combine_audio_video(ROOT_DIR, OUTPUT_DIR, '.m4a', '.webm', '.mkv')
```
