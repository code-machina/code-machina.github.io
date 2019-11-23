---
layout: post
title:  "Laravel 을 이용한 RESTful API 구현 (파트 1)"
subtitle: "Passport 를 이용한 인증 구현 1탄"
author: "코마 (gbkim1988@gmail.com)"
date:   2019-11-23 00:00:00 +0900
categories: [ "laravel", "php", "passport", "oauth" ]
excerpt_separator: <!--more-->
---

안녕하세요 **코마**입니다. 오늘은 Laravel 을 이용한 Restful API 구현의 시간을 가져보겠습니다. 이번 시간에는 passport 를 이용하여 간단한 인증을 구현해 보도록 하겠습니다. 😊 이 과정을 모두 마친 뒤에 여러분은 빠르게 로그인 기능을 만들어 활용할 수 있습니다. 자 그럼 시작해볼까요? 😺
 
<!--more-->

## 시작하기 전에

**코마**는 국가를 넘어 다양한 사용자들에게 좋은 정보를 제공하고자 부족하지만 영어를 동시에 기재하기로 결정하였습니다. 처음이라 부족한 부분이 많지만 너그러운 마음으로 읽어주시면 감사하겠습니다. 또한, 부족한 부분을 알려주시면 더욱 더 발전하는 모습을 보이도록 하겠습니다.

## 설치 (Installation)

라러벨 프로젝트를 구성하는 방법은 매우 간단합니다. 이제부터 저는 여러분이 OS X 를 사용하는 유저라고 가졍하여 설명을 드릴 것이며, `brew`, `composer`, `laravel`, `laravel/passport` 를 설치하고 Phpstorm 를 통해서 라러벨 프로젝트를 개발하는 방법에 대해 알려드리겠습니다. 그리고 중간에 php 문법을 간략히 설명함으로써 여러분들이 더욱 친근하게 php 에 다가갈 수 있도록 노력을 아끼지 않도록 하겠습니다.

<details><summary><strong>English</strong></summary>
Configuring a laravel project is quite easy. I assumed that you are going to use OS X. First, I tried to explain how to install a `brew`, `composer`, `laravel`, and `laravel/passport`. Second, Using Phpstorms, I will explain how to develop a laravel project. In the middle of posts, if necessary, Explaining a php syntax, I will try to let you be familiar with Php.
</details>

<br>
{% include advertisements.html %}
<br>

### 작업 순서

이번 글에서는 아래의 순서로 여러분에게 정보를 전달할 예정입니다.

1. brew 설치
2. php & composer & mysql@5.6 설치
3. laravel 설치
4. 프로젝트 생성
5. phpstorm 로드


<br>
{% include advertisements.html %}
<br>

## brew 설치

만약 여러분이 OS X 처음 사용한다면 이 글은 매우 유익할 것입니다. 우선 가장 필수적인 패키지 관리 툴인 `brew` 를 설치해보도록 하겠습니다. 아래의 명령을 복사하여 붙여넣으시면 됩니다.

<details><summary><strong>English</strong></summary>
If you are biginner at OS X(ten). This post will be helpful. First of all, I gonna install a necessary package management tool, which is Homebrew. Just copy & Paste it. 
</details>

<br>
{% include advertisements.html %}
<br>

- install homebrew

~~~bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
~~~

- update homebrew

~~~bash
brew update
~~~

- Add repositories

```bash
brew tap homebrew/dupes
brew tap homebrew/php
brew update
```

## php, composer, mysql 설치

brew 설치가 완료되었다면 이제부터 여러분은 원하는 프로그램을 자유롭게 설치할 수 있습니다. 이제 설치해야 할 소프트웨어의 목록은 아래와 같습니다. 

1. php@7.1
2. composer
3. mysql

특히 mysql 의 경우 brew 명령어를 통해서 서비스에 등록할 예정이니 참고해주세요.

<details><summary><strong>English</strong></summary>
If you installed Homebrew. You can easily install anything you want to install. Now we're going to install the below software list.
Especially, Note that I will use brew to register a mysql instance into service.
</details>


<br>
{% include advertisements.html %}
<br>

- install php 7.3

```bash
brew install php@7.3
```

- (un)linking php

> if you are already install other, You can unlink and link again.

```bash
brew unlink php@7.3
brew link php@7.2
```

- install composer

```bash
brew insall composer
```

- Add composer path to .bashrc or .zshrc

```bash
echo 'export PATH=$HOME/.composer/vendor/bin:$PATH' >> ~/.zshrc
```

- install mysql and start it

```bash
brew install mysql@5.7 # install `mysql 5.7`
brew tap homebrew/services # install brew services
brew services start mysql@5.7 # start mysql@5.7 as service
brew link mysql@5.7 --force # linking mysql@5.7
mysql -V # now, you can directly call `mysql` anywhere.
```

```bash
mysqladmin -u root password 'yoursecret'
```

## laravel 설치

이제 라러벨 공식 사이트에서 소개한대로 라러벨을 설치하고 프로젝트를 생성해보도록 하겠습니다.

<details><summary><strong>English</strong></summary>
According to laravel Website, I will install laravel and create `blog` sample project.
</details>

<br>
{% include advertisements.html %}
<br>

- Insall global laravel CLI

```bash
composer global require laravel/installer
```

- create new laravel project

```bash
laravel new blog
```

- Testing Project is work

```bash
php artisan serve
```

## Install laravel/passport

blog 프로젝트를 만들었으며 인증에 필요한 기능을 제공하기 위해 `laravel/passport` 모듈을 설치합니다. 그리고 mysql 에 접속하여 데이터베이스를 생성합니다.

<details><summary><strong>English</strong></summary>
I created new laravel project. now i will install passport plugin for an authentication. then To migrate Database, I will create blog database.
</details>

<br>
{% include advertisements.html %}
<br>

- Install passport

```bash
composer require laravel/passport
```

- Create Database

```bash
mysql -u root -p 
> create database blog;
> flush privileges;
```

```bash
php artisan migrate

Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table (0.02 seconds)
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table (0.01 seconds)
Migrating: 2016_06_01_000001_create_oauth_auth_codes_table
Migrated:  2016_06_01_000001_create_oauth_auth_codes_table (0.01 seconds)
Migrating: 2016_06_01_000002_create_oauth_access_tokens_table
Migrated:  2016_06_01_000002_create_oauth_access_tokens_table (0.02 seconds)
Migrating: 2016_06_01_000003_create_oauth_refresh_tokens_table
Migrated:  2016_06_01_000003_create_oauth_refresh_tokens_table (0.02 seconds)
Migrating: 2016_06_01_000004_create_oauth_clients_table
Migrated:  2016_06_01_000004_create_oauth_clients_table (0.01 seconds)
Migrating: 2016_06_01_000005_create_oauth_personal_access_clients_table
Migrated:  2016_06_01_000005_create_oauth_personal_access_clients_table (0.18 seconds)
Migrating: 2019_08_19_000000_create_failed_jobs_table
Migrated:  2019_08_19_000000_create_failed_jobs_table (0.01 seconds)
```

## Phpstorm 로드

프로젝트 생성이 완료되었으며 이제, phpstorm 에 로드하여 프로젝트를 구동하도록 하겠습니다. phpstorm 을 구동하고 생성한 프로젝트 파일을 열어주세요.


<details><summary><strong>English</strong></summary>
Everything is prepared. you can launch phpstorm and import created project into your workspace.
</details>

<br>
{% include advertisements.html %}
<br>


## 마무리

**Laravel 을 이용한 RESTful API 구현** 과정에서 프로젝트 초기 설정에 관하여 다루었습니다. 👏 (짝짝짝) 이 과정을 통해 여러분은 OS X 에서 Homebrew 를 설치하고 이를 통해 mysql, php, composer, laravel 을 구성하며 프로젝트를 생성할 수 있게되었습니다. 다음 시간에는 phpstorm 을 이용해 본격적으로 oauth 를 구현하도록 하겠습니다. 

아직 드릴 이야기가 무궁무진하니 좀 더 지켜봐주시면 더욱 감사할 것 같아요! 여러분이 laravel 을 장난감처럼 가지고 노는 그날까지 저 **코마**는 멈추지 않겠습니다. 대한민국 IT인 여러분들의 건승을 기원합니다.

지금까지 **코마** 였습니다.

구독해주셔서 감사합니다. 더욱 좋은 내용으로 찾아뵙도록 하겠습니다. 감사합니다.

<details><summary><strong>English</strong></summary>
Today, I handled How to configure laravel proejct. while I was explaining it, You might have some question. Don't you mind, Just leave your question. through this post, you will get a ability to set up a laravel project. in a next time, I will handle how to implement Oauth authentication in laravel. Thank you. 
</details>

<br>
{% include advertisements.html %}
<br>

## 링크 정리

이번 시간에 참조한 링크는 아래와 같습니다. 잘 정리하셔서 필요할 때 사용하시길 바랍니다.

- [Setup PHP and Composer on OSX via Brew](https://gist.github.com/shashankmehta/6ff13acd60f449eea6311cba4aae900a)
- [Install MySQL 5.7 on macOS using Homebrew](https://gist.github.com/operatino/392614486ce4421063b9dece4dfe6c21)
- [macOS 10.15 Catalina Apache Setup: Multiple PHP Versions](https://getgrav.org/blog/macos-catalina-apache-multiple-php-versions)