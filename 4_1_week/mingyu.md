---
title: Use WebP images
author: mingyu.gu
date: 2021.05.02
---

# Use WebP images

> 구글이 Webp를 만들어서 무료로 뿌리는 이유는 돈 때문이다. 구글 혼자서 엄청난 인터넷 트래픽을 사용하고 있는데, 이미지 포맷만 최적화해도 서버 트래픽이 크게 줄어 서버비를 아낄 수 있다. 게다가 인터넷 속도가 빨라질수록 광고 매출도 늘어나기 때문에 구글은 항상 인터넷 속도를 올리려고 노력한다.
>
> [WebP 제작 배경 :: 나무위키](https://namu.wiki/w/WebP)

webp 포멧의 이미지는 JPEG 및 PNG 이미지보다 작으며 일반적으로 파일크기가 25~35% 작습니다. 이로인해 First load times를 줄일 수 있습니다

- YouTube는 Thumbnail 이미지를 WebP로 전환한 결과 페이지로드 속도가 10% 빨라졌습니다.
- Facebook은 WebP를 사용하도록 전환했을 때 JPEG와 비교했을 경우 25 ~ 35%, PNG와 비교했을 경우 80%의 파일크기 절감을 경험했습니다.

WebP는 JPEG, PNG 및 GIF 이미지를 완전히 대체할 수 있으며, lossless and lossy compression 을 모두 지원합니다. `lossless compression` 은 데이터의 손실이 발생이고 `lossy compression`은 파일크기가 줄어들고 이미지 화질을 떨어지게 하는 압축입니다.

## Convert images to WebP

이미지를 WebP로 변환할 때 보통 [cwebp command-line tool](https://developers.google.com/speed/webp/docs/using) 와 [Imagemin WebP plugin](https://github.com/imagemin/imagemin-webp)을 사용합니다. Imagemin WebP plugin은 Webpack이나 Gulp와 같은 build tools를 사용하는 프로젝트에 최고의 선택이고 CLI는 image를 한 번만 변환시킬 때 사용하기 좋습니다.

이미지를 WebP로 변환할 때 다양한 압축 설정을 지정할 수 있지만 가장 중요하게 봐야할 것이 품질수준입니다. 0(최약)에서 100(최상)까지 품질 수준을 지정할 수 있기에 필요에 맞는 이미지 품질과 파일크기를 고려하여 선택하면 됩니다.

### Use cwebp

간단히 하나의 파일을 변경할 때는 cwebp를 사용하면 됩니다.

default:

```bash
cwebp images/flower.jpg -o images/flower.webp
```

using a query level of `50`:

```bash
cwebp -q 50 images/flower.jpg -o images/flower.webp
```

Convert all files in a directory:

```bash
for file in images/*; do cwebp "$file" -o "${file%.*}.webp"; done
```

### Use Imagemin

Imagemin은 Webpack과 같은 build tools를 사용하는 프로젝트에서 사용하기 좋습니다.

간단히 [imagemin-webp-webpack-plugin](https://www.npmjs.com/package/imagemin-webp-webpack-plugin)을 사용하면 될 것 같습니다.

```js
const ImageminWebpWebpackPlugin = require("imagemin-webp-webpack-plugin");

module.exports = {
  plugins: [new ImageminWebpWebpackPlugin()],
};
```

## Serve WebP images

만약 WebP를 제공하지 않는 [브라우저](https://caniuse.com/?search=webp)라면 fallback을 설정할 수도 있습니다.

#### Before:

```html
<img src="flower.jpg" alt="" />
```

#### After:

```html
<picture>
  <source type="image/webp" srcset="flower.webp" />
  <source type="image/jpeg" srcset="flower.jpg" />
  <img src="flower.jpg" alt="" />
</picture>
```

`<picture>`안의 `<source>` 및 `<img>` 태그는 상위에 대한 fallback입니다.

### picture

`<picture>`는 0개 이상의 `<source>`와 하나의 `<img>` 태그의 wrapper입니다.

### source

`<source>` 태그는 미디어 리소스를 지정합니다.

브라우저에 지원되는 형식을 순서대로 따라가며, 만약 지원하지 않는 경우 `<img>` 태그에 지정된 이미지로 대체됩니다.

### image

`<img>`의 경우 `<picture>` 태그를 지원하지 않는 경우 `<img>` 태그의 image를 로드하게 됩니다.

## Verify WebP usage

Lighthouse는 사이트의 모든 이미지가 WebP를 사용하지는 체크하고, 사용하지 않는 이미지를 나열합니다.

## Reference

- [Use WebP images :: WebDev](https://web.dev/serve-images-webp/)
