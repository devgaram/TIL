# CSS Media Query

- Title : CSS Media Query
- Date : 2020-09-19
- Category : 프론트


단말기의 유형(출력물 vs 화면)과 어떤 특성이나 수치(화면 해상도, 뷰포트 너비 등)에 따라 웹 사이트나 앱의 스타일을 수정할 때 유용하다.

## 기본 구문

```css
//
@media media-type and (media-feature-rule) {
  /* CSS rules go here */
}
```

### media-type

선택사항으로 기본값은 all

- `all`
- `print` 인쇄 결과물 및 출력 미리보기 화면에 표시 중인 문서
- `screen` 화면
- `speech` 음성 장치

### 논리 연산자

- `and`
- `not` 쿼리가 거짓일때만 참을 반환
- `only` 전체 쿼리가 일치할 때만 스타일 적용
- `,(쉼표)` or 연산자처럼 동작 하나만 true면 true

## 예제

### 고밀도 디스플레이를 위한 이미지 다루는 방법

고밀도 디스플레이 기기가 늘어나면서 웹에서도 고 해상도 이미지의 수요가 생겼다. 

`resolution` 은 고밀도 디스플레이를 위해 고안된 css문법으로, IE9를 포함한 브라우저 대부분이 지원합니다. 문법은 (min/max)-resolution: 값으로 사용되며, 기본 단위는 dppx입니다.
* 출력 장치의 픽셀 밀도
* 오페라미니와 IE는 dppx를 지원하지 않기 때문에 적당한 dpi 사용
* safari는 min/max-device-pixel-ratio 문법만 지원

```css
@mixin hidpi() {
  @media (-webkit-min-device-pixel-ratio: 1.5), (min-resolution: 144dpi) {
    @content;
  }
}

// 사용
@include hidpi {
	background-image: url(고해상도 이미지2x.png");
}
```

[https://blog.hanlee.io/2018/high-density-display-and-images/](https://blog.hanlee.io/2018/high-density-display-and-images/)

### 부트스트랩에서 미디어쿼리 어떻게 사용할까?

```css
@include media-breakpoint-up(sm) { ... }
@include media-breakpoint-up(md) { ... }
@include media-breakpoint-up(lg) { ... }
@include media-breakpoint-up(xl) { ... }

// 사용
@include media-breakpoint-up(sm) {
  .custom-class {
    display: block;
  }
}

@mixin media-breakpoint-up($name, $breakpoints: $grid-breakpoints) {
  $min: breakpoint-min($name, $breakpoints);
  @if $min {
    @media (min-width: $min) {
      @content;
    }
  } @else {
    @content;
  }
}

	@media (min-width: 576px) { ... }
	
	// Medium devices (tablets, 768px and up)
	@media (min-width: 768px) { ... }
	
	// Large devices (desktops, 992px and up)
	@media (min-width: 992px) { ... }
	
	// Extra large devices (large desktops, 1200px and up)
	@media (min-width: 1200px) { ... }

```