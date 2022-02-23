---
title: "[Hugo] 使用 GitHub Actions 部署 Hugo 网站"  
date: "2022-02-23"  
description: ""  
slug: ""  
image: ""  
categories:  
    - software
tags:  
    - Hugo
    - Blog
---  

## 设置自动化部署 hugo 的步骤

### 创建 GitHub Token
1. 从 [GitHub 的 Token 页面](https://github.com/settings/tokens/new) 创建一个**具有 repo 访问权限的 Token**。生成后会获得一个 40 字符长的 Token，将这个 Token 保存好(**注意：这个 Token 只会出现这一次，弄丢了是没办法找回的，只能重新生成**)
![github generate token page](github_generate_token.jpg)  

### 创建 GitHub Page 仓库
1. 在 GitHub 上创建一个名为 `<username>.github.io` 的仓库
2. 在仓库里添加一个 `index.html` 文件，里面输入 `Hello World` 保存
3. 在新的网页里访问 `https://<username>.github.io` 如果可以看到 `Hello World` 则仓库创建的没问题。

**注：上面所有的 `<username>` 替换为自己的 GitHub 用户名。**

### 创建 Hugo 站点仓库
1. 创建仓库，这一步没什么好说的，就是正常的创建一个普通的仓库(**注：一定是要 public 仓库**)。
2. 在仓库中点击 `Settings -> Secrets` 填入第一步创建的 Token (可以通过 `https://github.com/<username>/<repo-name>/settings/secrets` 访问)。
![github repo add secret](github_repo_add_secret.jpg)  
3. 克隆到本地。

### 创建 GitHub 操作
1. 在 hugo 站点仓库中添加 `.github/workflows/main.yml` (`main.yml` 文件名没有限制)
```
name: CI
on: push
jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - name: Git checkout
        uses: actions/checkout@v2

      - name: Update theme
        # (Optional)If you have the theme added as submodule, you can pull it and use the most updated version
        run: git submodule update --init --recursive

      - name: Setup hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "0.64.0"
          extended: true

      - name: Build
        # remove --minify tag if you do not need it
        # docs: https://gohugo.io/hugo-pipes/minification/
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.TOKEN }}
          external_repository: <username>/<username>.github.io
          publish_dir: ./public
          # keep_files: true
          user_name: <username>
          user_email: <username@email.com>
          publish_branch: master
          # cname: example.com
```
  - 在这 `Git checkout` 一步中，我们将获取包含 Hugo 站点的存储库的最新代码。
  - 在这 `Setup hugo` 一步中，我们将使用 `peaceiris/actions-hugo` 来安装 Hugo。您需要指定要使用的 hugo 版本。建议使用本地机器的 hugo 版本（命令：）`hugo version`。
  - 在这 `Build` 一步中，我们将使用 `hugo --minify` 命令构建静态内容。通过使用 `--minify`，我们将缩小站点中的资产。有关更多信息，请查看 [hugo 文档](https://gohugo.io/hugo-pipes/minification/)。
  - 最后 `Deploy` 一步。现在我们将部署最后一步的静态内容。我们将使用 [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages) 操作来运行部署。在这里，我们使用，`external_repository: <username>/<username>.github.io` 因为否则静态内容将被推送到同一个 repo 中（在不同的分支中）。当我们指定外部存储库时，静态内容将被推送到 `<username>.github.io`. 对于这一步，我们将使用我们在第一步中指定的个人 Token。如果您取消注释 `keep_files: true`，则部署将保留旧文件 `<username>.github.io`，否则它将替换所有内容。最后，如果您有自定义域，则 `cname` 需要进行配置。有关更多信息，请查看 [GitHub 市场中的文档](https://github.com/marketplace/actions/github-pages-action)。

### 添加 hugo 站点代码
1. 创建 hugo 的结构，可以使用 `hugo new site xxx` 创建一个默认的站点目录，将其目录结构拷到仓库中。
2. 如果添加了主题，可以考虑将其添加为子模块，这样在部署前能方便的将主题更新为最新的版本(上面的 `main.yml` 中的步骤 `Update theme`，已经实现了在部署之前更新主题的版本)，下面命令以主题 `hugo-theme-stack` 为例：
```
git submodule add https://github.com/CaiJimmy/hugo-theme-stack.git themes/stack
```

3. 这时基本就都准备好了，但为了方便看效果，可以将主题中提供的示例部署出来。示例的路径在主题根路径下的 `exampleSite` 中(例如: `themes/stack/exampleSite`)，将里面的 `content` 和 `config.yaml`(或 `config.toml`)拷出来放在仓库的根路径下就行了。
4. 提交代码。

**hugo 的搭建与测试详细的内容可以查看参考资料资料中的内容，这里就不详细记录了。**

### 推送到 GitHub
1. 执行 `git push` 将 hugo 站点代码推送到 GitHub，可以在 GitHub 对应的仓库页面上的 `Actions` 看到 Actions 的进度。
![github repo actions](github_repo_actions.jpg)

## 参考资料
[Hugo：使用 GitHub Actions 部署静态站点](https://ruddra.com/hugo-deploy-static-page-using-github-actions/)  
[GitHub Pages Action](https://github.com/peaceiris/actions-gh-pages)  
[使用 Hugo 快速搭建个人博客记录](https://blog.wangjunfeng.com/post/hugo/)  
[知识共享 4.0 许可的明文版本](https://creativecommons.org/2014/01/07/plaintext-versions-of-creative-commons-4-0-licenses/)  
