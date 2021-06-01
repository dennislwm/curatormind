- Repurpose a YouTube Playlist into an Anchor.fm Podcast
  
  TL;DR
  
  There are two separate parts to this project:
  
  1. Executing our custom GitHub action from YouTube
  1. Executing the third-party GitHub action to Anchor.fm
- Introduction
  
  **TL;DR**
- Prerequisites
  
  * Anchor.fm test account
  * GitHub account
  * youtube-dl
  * [FFmpeg](https://ffmpeg.org)
  * youtube-to-anchorfm
  * rss2youtube-dl
- Step 1 - Execute GitHub Action from YouTube
  
  This custom GitHub action will download the latest episode from YouTube, check if id is not a duplicate, then update id on the `episode.json` file.
  
  We use the term **video** for a file in a *series* downloaded from YouTube, while the term **episode** refers to a file in a *series* uploaded to Anchor.fm.
  
  **How it works**
  
  The workflow is using `youtube-dl` library, and the action will start on a schedule.
  
  **Setup GitHub Repository**
  
  Fork the GitHub repo from [`Schrodinger-Hat/youtube-to-anchorfm`](https://github.com/Schrodinger-Hat/youtube-to-anchorfm).
- Step 2 - Execute GitHub Action to Anchor.fm
  
  This third-party GitHub action will upload an audio file from a given YouTube video automatically to your Anchor.fm account.
  
  **How it works**
  
  The workflow is using `youtube-dl` library and `puppeteer`. 
  
  The action will start everytime you push a change on the `episode.json` file. The action uses a Docker image built over Ubuntu 18.04.
  
  **How to use**
  
  In your repository root directory, you should add a episode.json file containing your YouTube video id, e.g.
  
     {
       "id": "nHCXZC2InAA"
     }
  
  Then you can add under the `.github/workflows` directory this yml
  
     #+-----------------------------------------------------------------------------------------+
     #|                                M A I N   W O R K F L O W                                |
     #+-----------------------------------------------------------------------------------------+
     name: toanchor
     #+-----------------------------------------------------------------------------------------+
     #|                            E X T E R N A L   T R I G G E R S                            |
     #+-----------------------------------------------------------------------------------------+
- Controls when the action will run. Triggers the workflow on push or pull request
- events but only for the master branch
- * * * * *  UTC (Convert to Singapore: +0800)
- ┬ ┬ ┬ ┬ ┬
- │ │ │ │ └───── day of week (0 - 7) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
- │ │ │ └────────── month (1 - 12)
- │ │ └─────────────── day of month (1 - 31)
- │ └──────────────────── hour (0 - 23)
- └───────────────────────── min (0 - 59)
     on:
       push:
         paths:
	- episode.json
	           branches: [master]
	         schedule:
	- cron:  '1 0 * * *'
	       jobs:
	         #+-----------------------------------------------------------------------------------------+
	         #|                                 C U S T O M   J O B                                     |
	         #+-----------------------------------------------------------------------------------------+
	         upload_episode:
	           #----------------------------------------
- Type of runner that the job will run on
         runs-on: ubuntu-latest
         steps:
           #---------------------------------------------------------
- Runs third-party actions
- Checks out your repository under $GITHUB_WORKSPACE
	- uses: actions/checkout@v2
	             #------------------------------------
- Runs third-party actions
- Upload episode from YouTube to Anchor.Fm
	- name: upload_episode
	  uses: Schrodinger-Hat/youtube-to-anchorfm@v0.1.5
	  env:
	    ANCHOR_EMAIL: ${{ secrets.ANCHOR_EMAIL }}
	    ANCHOR_PASSWORD: ${{ secrets.ANCHOR_PASSWORD }}..
- Step 3 - Troubleshooting
  
  We identify any possible cause and effect of a breakdown.
  
  **Possible Breakdown**
  
  * fails to download a new video from YouTube (step 1), which leads to no new episode to upload to Anchor.fm (step 2)
  
  This breakdown can be easily recovered by fixing the problem, then resuming from the previous video in series.
  
  * downloads more than one videos from YouTube (step 1), which leads to skipping an episode on upload to Anchor.fm (step 2)
  
  This breakdown can be recovered if we keep track of which **video** has been processed, and which **episode** has been uploaded.
  
  **Possible Solution**
  
  Maintain two separate text files:
  
  * a list of **video** ids which has been download from YouTube
  * a list of **episode** ids which has been uploaded to Anchor.fm
  
  Use `diff` command to check which episodes have not been uploaded to Anchor.fm.
- Step 4 - Add Podcasts from Existing Videos
  
  This step is necessary if there are existing videos in your YouTube playlist.
  
  * Download metafile with date-id as filename using youtube-dl
  * Verify and write id into text file using custom script
  * Batch download a video and post-process it to an audio via ffmpeg using youtube-dl
  * Upload audio as podcast to Anchor.fm
  
  **youtube-dl.conf**
  
  There are numerous options available in the configuration file, below are some important download options:
  
  * `--ignore-errors` Continue on download errors as some playlists contain private videos
  * `--config-location PATH` Location of the configuration file; either the path to the config or its containing directory.
  * `--max-downloads NUMBER` Abort after downloading NUMBER files
  * `--datebefore DATE` Download only videos uploaded on or before this date YYYYMMDD (i.e. inclusive)
  * `--dateafter DATE` Download only videos uploaded on or after this date YYYYMMDD (i.e. inclusive)
  * `--skip-download` Do not download the video
  
  Download a metafile with date-id as its filename in one batch, skipping download of video. The site [https://dennislwm.github.io/docker-youtube-dl](https://dennislwm.github.io/docker-youtube-dl/) has a starter list of YouTube videos.
  
  *Warning*: Do not download current videos that are in the YouTube RSS feed (see next section), as this will create duplicate podcasts.
  
  Alternatively, create one config file per batch, e.g. *youtube-dl-xxx.conf*, and use `--config-location` to specify which batch to run. This ensures that we can isolate any problems that occur.
  
  These are some important post-processing options:
  
  * `--extract-audio` Convert video files to audio-only files (requires ffmpeg/avconv and ffprobe/avprobe)
  * `--audio-quality QUALITY` Specify ffmpeg/avconv audio quality, insert a value between 0 (better) and 9 (worse) for VBR or a specific bitrate like 128K (default 5)
  * `--ffmpeg-location PATH` Location of the ffmpeg/avconv binary; either the path to the binary or its containing directory.
  * `--keep-video` Keep the video file on disk after the post-processing; the video is erased by default
  
  Optionally, post-process a video by extracting the audio in the best format and highest quality via ffmpeg, then erasing the video.
- Lines starting with # are comments
     
     #--------
- OPTIONS
- Ignore errors caused by private videos, etc
     --ignore-errors
- Location of the configuration file; either the path to the config or its containing directory.
     #--config-location PATH
     
     #---------------
- VIDEO SELECTON
- Abort after downloading NUMBER files
     #--max-downloads NUMBER
     
     # Download only videos uploaded on or before this date YYYYMMDD (i.e. inclusive)
     #--datebefore 20151231
- Download only videos uploaded on or after this date YYYYMMDD (i.e. inclusive)
     #--dateafter 20200223
- Download only the video, if the URL refers to a video and a playlist.
     --no-playlist
     
     #-----------------
- DOWNLOAD OPTIONS
- Size of download buffer (e.g. 1024 or 16K) (default is 1024)
     --buffer-size 4096
     
     #-------------------
- FILESYSTEM OPTIONS
- Use only video ID in file name
     #--id
- Allows users to indicate a template for the output file names.
     -o "%(upload_date)s%(id)s.%(ext)s"
- Restrict filenames to only ASCII characters, and avoid "&" and spaces in filenames
     #--restrict-filenames
- Do not overwrite files
     --no-overwrites
- Write video metadata to a .info.json file      
     --write-info-json
- Display progress in console titlebar
     --console-title
     
     #-------------------------------
- VERBOSITY / SIMULATION OPTIONS
     
     # Do not download the video
     --skip-download
- Print various debugging information
     --verbose
- Print downloaded pages encoded using base64 to debug problems (very verbose)
- (warning: this will yield a rather large output, redirect it to the file log.txt by adding >log.txt 2>&1 to your command-line)
     #--dump-pages
- Write downloaded intermediary pages to files in the current directory to debug problems
     #--write-pages
     
     #------------------------
- POST-PROCESSING OPTIONS
- Convert video files to audio-only files (requires ffmpeg/avconv and ffprobe/avprobe)
     #--extract-audio
- Specify audio format: "best", "aac", "flac", "mp3", "m4a", "opus", "vorbis", or "wav"; "best" by default; No effect without -x
     #--audio-format mp3
- Specify ffmpeg/avconv audio quality, insert a value between 0 (better) and 9 (worse) for VBR or a specific bitrate like 128K (default 5)
     #--audio-quality 0
- Keep the video file on disk after the post-processing; the video is erased by default
     #--keep-video
- Embed subtitles in the video (only for mp4, webm and mkv videos) requires ffmpeg or avconv
     #--embed-subs
- Location of the ffmpeg/avconv binary; either the path to the binary or its containing directory.
     #--ffmpeg-location PATH
  
  **rfeed**
- Step 5 - Create RSS Feed from Metafile
- Step 6 - Update Podcast Daily using RSS Feed
  
  This step is required after adding the initial podcasts to **Anchor.fm**. 
  
  * Clone the files from [dennislwm/rss2youtube-dl](https://github.com/dennislwm/rss2youtube-dl)
  * Create a Dockerfile
  
  **Dockerfile**
  
     FROM python:3.7-slim
     RUN apt-get update
     RUN apt-get --assume-yes install python-requests
     #--------------------------------------------------
- GitHub Action ignores WORKDIR, ENTRYPOINT and CMD
     WORKDIR /app
     COPY requirements.txt .
     RUN pip install --no-cache-dir -r requirements.txt
     COPY . .
     ENTRYPOINT [ "python3" ]
     CMD [ "rss2youtube-dl" ]
  
  **RSS feed for a YouTube playlist**
  
  To get the updated feed url use this:
  
     https://www.youtube.com/feeds/videos.xml?playlist_id=YOURPLAYLISTIDHERE
  
  *Warning*: The RSS feed appears to have a maximum of 15 entries.
  
  * Edit `.channels.yaml` file
  
     example:
         name: Homilies
         download_dir: download
         rss: [https://www.youtube.com/feeds/videos.xml?playlist_id=PLuK5ZVXOaYHRkonVMKe66LF7eVbFcXcgV](https://www.youtube.com/feeds/videos.xml?playlist_id=PLuK5ZVXOaYHRkonVMKe66LF7eVbFcXcgV) 
  
  * Create a folder `.seen` within the same folder as the yaml file.
- Step 7 - Rewrite GitHub Action as Python Application
  
  The limitation of the GitHub action youtube-to-anchorfm is that it allows one episode per input json file. However, we can rewrite this in Python but with additional features:
  
  * Be able to store more than one episode per input json (or text) file - this ensures that a duplicate id is not re-used
  * Adding podcasts from an existing YouTube playlist and updating a podcast daily using an RSS feed is seamless and do not require separate GitHub actions.
- Conclusion
  
  In this article, you have succesfully achieved:
  
  *
- Get the Source Code
  
  You can download the above source code from GitHub repository [dennislwm/youtube-to-anchorfm](https://github.com/dennislwm/youtube-to-anchorfm).
- What To Do Next
  
  You can further explore this project in several meaningful ways:
  
  * Host an API youtube2audio-as-a-service
  * Design an API vimeoyoutube2audio-as-a-service
  * Design an API uploadvideo2audio-as-a-service
  * Design a Micro-Saas rssyoutube2anchorfm