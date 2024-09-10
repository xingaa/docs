---
# https://vitepress.dev/reference/default-theme-home-page
layout: home

hero:
  name: "兴"
  text: "不要懊恼，这是一个广大的世界"
  tagline: 只有当不幸真的到来时，我们才会怀念和渴望那些逝去的美好时光。<span style="color:red">---叔本华-</span>
  actions:
    - theme: brand
      text: Markdown Examples
      link: /markdown-examples
    - theme: alt
      text: API Examples
      link: /api-examples
  image:
      src: /logo.svg
      alt: VitePress

features:
  - icon: 🎨
    title: Feature A
    details: Lorem ipsum dolor sit amet, consectetur adipiscing elit
  - icon: 🤿 
    title: Feature B
    details: Lorem ipsum dolor sit amet, consectetur adipiscing elit
  - title: Feature C
    icon: 🤡
    details: Lorem ipsum dolor sit amet, consectetur adipiscing elit
---

<style>
:root {
  --vp-home-hero-name-color: transparent;
  --vp-home-hero-name-background: -webkit-linear-gradient(120deg, #bd34fe 30%, #41d1ff);

  --vp-home-hero-image-background-image: linear-gradient(-45deg, #bd34fe 50%, #47caff 50%);
  --vp-home-hero-image-filter: blur(44px);
}
.image-src{
  width: 160px;
  height: 160px;
}
@media (min-width: 640px) {
  :root {
    --vp-home-hero-image-filter: blur(56px);
  }
 
}

@media (min-width: 960px) {
  :root {
    --vp-home-hero-image-filter: blur(68px);
  }
  .image-src{
    width: 240px;
    height: 240px;
  }
}
</style>