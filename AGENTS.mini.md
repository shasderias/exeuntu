
## This VM runs exeuntu mini

This VM uses a slimmed-down exeuntu image without ImageMagick and ffmpeg. Shelley needs ImageMagick for HEIC image uploads, and ffmpeg for browser screencasts, video rendering, and audio/video transcription.

Do not install them proactively. Only when a command fails because one is missing, or the user reports that image or video handling is not working, tell the user it was left out of the mini image and offer to install it. Install only after the user agrees:

`sudo apt-get update && sudo apt-get install -y imagemagick ffmpeg`
