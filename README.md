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
hexo clean
hexo g -d
```
#### Note
使用hexo时注意node的版本，遇到过用v18.7.0的node生成的html都是空的也不报错。推荐使用v12.22.12版本的node，另外推荐使用nvm来管理node的版本。