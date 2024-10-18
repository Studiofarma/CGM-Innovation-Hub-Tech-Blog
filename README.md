# CGM-Innovation-Hub-Tech-Blog

[CGM Innovation Hub - Compugroup Medical Italy Tech Blog](https://studiofarma.github.io/CGM-Innovation-Hub-Tech-Blog/)

## How to install and configure Jekyll to serve this blog

### Windows

1. Download and install [Ruby for Windows](https://rubyinstaller.org/). (I am on `ruby 3.2.5 (2024-07-26 revision 31d0f1a2e7) [x64-mingw-ucrt]`)
2. Open a shell, clone this repository and cd into `CGM-Innovation-Hub-Tech-Blog`.
3. Run these commands:
```powershell
    bundle install
    bundle update
    bundle exec jekyll serve --watch --trace
```