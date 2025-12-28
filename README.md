# Kindle Modding Wiki

*Created by [Hackerdude](https://ko-fi.com/hackerdude)*

*Theme by [Penguins184](https://ko-fi.com/penguins186)*

## Contributing

Look at `content/sample.md.example` to see how you can write/format a page.

1. Clone this Repository
2. Get Hugo
3. Run `hugo server`

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
