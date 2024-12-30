# Blog using Hugo

## Deploy
To publish the static site:
```
hugo
```

We deploy to Github Pages using [this](.github/workflows/hugo.yaml) Github action.


## Development
To serve locally:
```bash
hugo server
```

### Adding a video to the blog

Add to `static/videos`.

If MP4 from Google Photos, first convert it with ffmpeg:
```bash
ffmpeg -i static/videos/video.mp4 -vcodec libx264 -acodec aac static/videos/video2.mp4
```