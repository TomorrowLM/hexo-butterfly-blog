# hexo-butterfly-blog

node 12.16.1
- 下载依赖

- 下载主题

  git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly

- 下载hexo-cli

img路径
需要替换hexo-asset-image index.js文件
https://github.com/xcodebuild/hexo-asset-image/blob/master/index.js

```
link = link.replace(/.io\//g,"");
console.log(config.root ,'*****',   link , '*****',src);
console.log(config.root + link + src);
$(this).attr('src', config.root  + 'img/' + src);

// $(this).attr('src', config.root + link + src);
```



  ```
  npm install -g hexo-cli
  ```

hexo new page music

- hexo g编译

- hexo s本地启动服务

- hexo d部署