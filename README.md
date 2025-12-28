# Kindle Modding Wiki

*Created by [Hackerdude](https://ko-fi.com/hackerdude)*

*Theme by [Penguins184](https://ko-fi.com/penguins186)*

*Website here [https://kindlemodding.org/](https://kindlemodding.org/)*

## Contributing

Look at `content/sample.md.example` to see how you can write/format a page.

1. Clone this Repository
2. Get [Hugo](https://gohugo.io/)
3. Get [Dart Sass](https://sass-lang.com/dart-sass/)
4. Run `python static/models_json_generator.py`
5. (Optional) Run `wget https://github.com/KindleModding/jb.sh/releases/latest/download/jb.sh -O ./static/jb.sh` 
6. Run `hugo serve`

## Development

### Local server

```sh
hugo server -DF --noHTTPCache --bind 0.0.0.0
```

### Local server under subpath

```sh
hugo server -DF --noHTTPCache --bind 0.0.0.0 --baseURL http://0.0.0.0/kindlewiki
```

### Build
```sh
hugo --gc --minify
```
