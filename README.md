## git clone 해서 작업할 경우

```bash
git submodule init
git submodule update
```

## post 생성
```bash
hugo new content content/posts/my-post.md
```

## giscus comments

```html
<script src="https://giscus.app/client.js"
        data-repo="dazeddd/tech-blog-comments"
        data-repo-id="R_kgDOKol98A"
        data-category="General"
        data-category-id="DIC_kwDOKol98M4CapgL"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="ko"
        crossorigin="anonymous"
        async>
</script>
```