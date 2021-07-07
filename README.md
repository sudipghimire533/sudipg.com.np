## Website source hosted under [sudipg.com.np](https://sudipg.com.np)![build-status](https://api.netlify.com/api/v1/badges/e19a9c8d-bc7f-460c-8eb3-1625b111abd8/deploy-status)

## Quick Info:
- ### _Site generator_: [Hugo](https://gohugo.io)
- ### _Hugo Theme_: [hugo theme bootstrap](https://github.com/razonyang/hugo-theme-bootstrap)
- ### _Article Content_: Markdown formatted under [/content/en/posts](https://gitlab.com/sudipghimire533/sudipg.com.np/-/tree/main/content/en/posts)
- ### _public_: Yes [Post your writing](https://sudipg.com.np/en/write)


## Writing from browser
* head to [Gitlab web ide](https://gitlab.com/-/ide/project/sudipghimire533/sudipg.com.np/edit/main/-/content/en/posts/)
* Create a markdown file under posts directory and start writing
* Get [More information](https://sudipg.com.np/en/write)


## Setup local environment
1. Install Hugo. [Refer to official instructions](https://gohugo.io/getting-started/installing/).
__Note: Current theme depends on extended version of hugo__
2. Clone and initilize the repo
```
git clone https://gitlab.com/sudipghimire533/sudipg.com.np  ThePage
cd ThePage
git submodule init
```
3. Create new Article
```
hugo new posts/article-title
```
4. `content/posts/article-title.md` file will generate with some default. Write your article in this file
5. Preview the article
```
hugo serve --disableFastRender --buildDrafts
```
6. For more information about developing content on Hugo visit [Hugo official site](https://gohugo.io) and for instruction to build and  modify theme follow instruction from [razonyang/hugo-theme-bootstrap](https://github.com/razonyang/hugo-theme-bootstrap)
7. Submit the article
```
git push
```

## Credits
Initiated by [Sudip Ghimire](https://me.sudipg.com.np/) using [Theme from razanyang](https://github.com/razonyang/hugo-theme-bootstrap) using [Hugo](https://gohugo.io).
