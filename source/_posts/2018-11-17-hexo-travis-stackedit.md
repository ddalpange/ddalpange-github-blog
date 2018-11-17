---
title: 블로그를 편하게 하기 위한 발악 (hexo, travis, stackedit)
tags: [hexo, travis, github, stackedit]
categories:
date: 2018-11-16 18:39:55
---

티스토리를 커스터마이징 하여 사용하다가 혈압이 올라서 다시 헥소로 포스팅을 하기로 했다.

티스토리는 자체 에디터를 사용하는데 html 결과물이 나오는 형태가 일관성이 없어서 테마를 바꿀때도 또 바꾸어줘야하고 심지어 페이지를 작성하는 에디터와 포스트를 작성하는 에디터가 달라서 포멧이 다르다. Gallery 등 다양한 툴을 붙여서 커스터마이징 하고 싶었는데 혈압올라서 그만뒀다

그래서 티스토리의 단점을 커버하기보다 헥소의 단점을 커버하는게 더 낫다고 생각하여 변경하였다.

헥소의 문제점은 두가지다.
1. `hexo deploy -g` 를 매번 해줘야한다.
2. 개발환경이 갖추어지지 않으면 포스팅을 할 수 없다.

그래서 이 두가지 문제를 해결하는 방안을 포스팅하려한다.

## 1. [Travis CI](https://travis-ci.org/)를 사용하여 자동 빌드 및 푸쉬하기
![Travis CI screenshot](https://cdn.travis-ci.org/images/landing-page/laptop-f308ed79defa4f49c5f01af29a60084d.png)

앱의 빌드 및 배포를 도와주는 CI 툴이다. 완전 무료이고 사용법도 굉장히 쉽다.

```
# .travis.yml
language: node_js
node_js:
- "9"

branches:
	only:
		- master

install:
- npm install

before_script:
- git config --global user.name 'ddalpange'
- git config --global user.email 'ddalpange@gmail.com'
- sed -i "s/__GITHUB_TOKEN__/${__GITHUB_TOKEN__}/" _config.yml

script:
- hexo clean
- hexo generate
- hexo deploy
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTg0NjM4OTUzNl19
-->