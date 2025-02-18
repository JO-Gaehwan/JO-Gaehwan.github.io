---
title : Making Chirpy Theme Github blog
date : '2025-02-18'
categories : [ETC]
tags : [chirpy, github, blog, git, gitblog, vscode]
---

이 문서는 Ubuntu 22.04와 Mac OS 기준으로 Chirpy Theme을 이용하여 Github blog를 구축하는 방법을 설명한다. Chirpy Theme은 Ruby 3.1 이상이 필요하므로, rbenv라는 Ruby 버전 관리 도구로 Ruby를 설치한다.

---

## Ubuntu 22.04 기준

### 1. 필수 패키지 설치

Ubuntu에서는 다음 명령어로 필수 패키지를 설치한다.

```bash
sudo apt update
sudo apt install -y git curl autoconf bison build-essential libssl-dev libyaml-dev libreadline-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm-dev
```

### 2. rbenv 및 ruby-build 설치

1. **rbenv 클론 및 환경변수 설정 (Bash 기준):**

   ```bash
   git clone https://github.com/rbenv/rbenv.git ~/.rbenv
   echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
   echo 'eval "$(rbenv init -)"' >> ~/.bashrc
   source ~/.bashrc
   ```

2. **ruby-build 플러그인 설치**  
   ruby-build는 rbenv를 통해 Ruby를 설치할 수 있게 해주는 플러그인

   ```bash
   git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
   ```

3. **가용한 Ruby 버전 확인**  
   다음 명령어로 rbenv에서 설치 가능한 Ruby 버전 목록을 확인

   ```bash
   rbenv install -l
   ```

   목록에서 원하는 Ruby 버전을 선택한다. 본 문서에서는 3.3.7 버전을 설치한다.

4. **Ruby 설치 및 전역 설정**

   ```bash
   rbenv install 3.3.7
   rbenv global 3.3.7
   ```

   `ruby -v` 명령어로 Ruby 버전이 3.3.7로 출력되면 정상

### 3. Bundler와 Jekyll 설치

Ruby 환경이 준비되었으면, 다음 명령어로 Bundler와 Jekyll을 gem으로 설치

```bash
gem install bundler jekyll
```

### 4. Chirpy 테마 Fork 및 클론

1. **Fork하기**  
   웹 브라우저에서 Chirpy 공식 저장소  
   ([https://github.com/cotes2020/jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy))에 접속하여 우측 상단의 **Fork** 버튼을 클릭해 본인 계정으로 Fork

2. **클론하기**  
   Fork한 저장소를 로컬로 클론한다. 웬만하면 HTTPS 대신 SSH를 사용한다. (SSH가 permission 설정에 더 편리하다.)

   ```bash
   git clone git@github.com:your_username/jekyll-theme-chirpy.git
   cd jekyll-theme-chirpy
   ```

   *`your_username`은 본인의 GitHub 사용자 이름으로 변경한다.*

3. **초기화 스크립트 실행**  
   저장소 루트에서 초기화 스크립트를 실행한다.

   ```bash
   ./tools/init.sh
   ```

### 5. 프로젝트 의존성 설치

저장소 루트에 있는 `Gemfile`에 명시된 Ruby Gem들을 다음 명령어로 설치한다.

```bash
bundle install
```

### 6. 로컬에서 블로그 미리보기

설정 및 커스터마이징 후, 로컬 서버에서 사이트를 확인한다.

```bash
bundle exec jekyll serve
```

터미널에 표시되는 주소(일반적으로 [http://localhost:4000](http://localhost:4000))에 접속하여 블로그가 정상적으로 보이는지 확인한다. 파일 수정 시 자동으로 재빌드됨

### 7. 변경사항 커밋 및 GitHub Pages 배포

변경사항을 Git에 커밋한 후, GitHub Pages로 배포

1. **GitHub Pages 설정**  
   - GitHub 저장소 페이지에서 **Settings > Pages**로 이동한다.  
   - 배포할 브랜치(예: `main` 또는 `gh-pages`)를 선택한다.  
   - Build and Deployment에서 Source를 GitHub Actions로 선택한다.  
   - 잠시 후 GitHub Pages가 활성화되며, [https://your_username.github.io](https://your_username.github.io) (또는 프로젝트 페이지 URL)에서 블로그를 확인할 수 있다.

2. **GitHub Actions Workflow 수정**  
   `.github/workflows/pages-deploy.yml` 파일에서 build site 단계 위에 npm build 단계를 추가한다. 예시는 아래와 같다.

   ```yaml
   - name: npm build
     run: npm install && npm run build
   - name: Build site
     run: bundle exec jekyll b -d "_site${{ steps.pages.outputs.base_path }}"
   ```

---

## Mac OS 기준

Mac OS에서는 Homebrew를 이용하여 필요한 패키지와 rbenv를 설치한다.

### 1. Homebrew 설치 (미설치 시)

Homebrew가 설치되어 있지 않다면, 다음 명령어로 설치한다.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

설치 후, Homebrew가 정상적으로 동작하는지 확인한다.

### 2. rbenv 및 ruby-build 설치

Homebrew를 이용하여 rbenv와 ruby-build를 설치한다.

```bash
brew update
brew install rbenv ruby-build
```

설치 후, rbenv 초기화를 위해 사용하는 셸에 맞게 설정 파일에 추가한다.  
Bash를 사용하는 경우:

```bash
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
source ~/.bash_profile
```

Zsh를 사용하는 경우:

```bash
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc
```

가용한 Ruby 버전은 다음 명령어로 확인한다.

```bash
rbenv install -l
```

원하는 Ruby 버전을 선택하여 설치한다. 본 문서에서는 3.3.7 버전을 설치한다.

```bash
rbenv install 3.3.7
rbenv global 3.3.7
```

`ruby -v` 명령어로 Ruby 버전이 3.3.7로 출력되면 정상이다.

### 3. Bundler와 Jekyll 설치

Ruby 환경이 준비되었으면, 다음 명령어로 Bundler와 Jekyll을 설치한다.

```bash
gem install bundler jekyll
```

### 4. Chirpy 테마 Fork 및 클론

Chirpy 테마를 Fork하고 클론하는 과정은 Ubuntu와 동일하다.

1. 웹 브라우저에서 Chirpy 공식 저장소  
   ([https://github.com/cotes2020/jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy))에 접속하여 본인 계정으로 Fork한다.
2. SSH를 이용하여 Fork한 저장소를 로컬로 클론한다.

   ```bash
   git clone git@github.com:your_username/jekyll-theme-chirpy.git
   cd jekyll-theme-chirpy
   ```

3. 초기화 스크립트를 실행한다.

   ```bash
   ./tools/init.sh
   ```

### 5. 프로젝트 의존성 설치

저장소 루트에 있는 `Gemfile`에 명시된 Ruby Gem들을 다음 명령어로 설치한다.

```bash
bundle install
```

### 6. 로컬에서 블로그 미리보기

다음 명령어로 로컬 서버에서 사이트를 확인한다.

```bash
bundle exec jekyll serve
```

터미널에 표시되는 주소(일반적으로 [http://localhost:4000](http://localhost:4000))에 접속하여 블로그가 정상적으로 보이는지 확인한다.

### 7. 변경사항 커밋 및 GitHub Pages 배포

변경사항을 Git에 커밋한 후, GitHub Pages로 배포하는 절차는 Ubuntu 기준과 동일하다.

---

이상으로 Ubuntu 22.04와 Mac OS 기준으로 Chirpy Theme을 이용하여 Github blog를 만드는 방법을 정리하였다.