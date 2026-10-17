# yangguang8112.github.io
每次提交代码后再部署博客

#### 提交代码
``` bash
git add .
git commit -m 'something'
git push origin hexo
```
#### 部署博客
``` bash
nvm use          # 读取 .nvmrc，切到 v12.22.12
hexo clean
hexo g -d        # 生成并推送到 master 分支（GitHub Pages 从这里发布）
```
#### Note
使用hexo时注意node的版本，遇到过用v18.7.0的node生成的html都是空的也不报错。推荐使用v12.22.12版本的node，另外推荐使用nvm来管理node的版本。仓库根目录的 `.nvmrc` 已固定为 `12.22.12`，`nvm use` 会自动选中。

#### 目录结构
站点是 Hexo 博客（主题 `themes/huno`）。

| 路径 | 是否发布 | 说明 |
| --- | --- | --- |
| `source/` | 是 | 博客正文、`/about` 页面、站点图片 |
| `source/about/` | 是 | 博客的「关于」页，独立静态页面（见下） |
| `resume/` | **否** | 个人简历，只在本地看，不会进 `public/` |
| `_archive/` | 否 | 历史简历页面与旧模板产物，留档用 |
| `themes/huno/` | 是 | 主题。自定义覆盖写在 `source/css/overrides.css` |

#### /about 与 resume/
`/about` 是独立静态页面（`source/about/index.html`，样式与脚本内联，不依赖主题），
靠 `_config.yml` 里的 `skip_render: about/**` 让 Hexo 原样拷贝而不做模板渲染。
**挪动这个目录时记得同步改 `skip_render`**，否则 Hexo 会把它当模板渲染而报错。
它用到的图片在 `source/about/img/`，站点级公共图片在 `source/img/`（页面里用 `../img/...` 引用）。

`resume/` 在仓库根目录、**不在 `source/` 下，所以不会被部署**，但受 git 跟踪。
直接双击 `resume/index.html` 就能在浏览器里打开（资源路径都是相对的，图片借用
`../source/img/`），打印 / 另存为 PDF 用。想改版式就编辑这个文件。
