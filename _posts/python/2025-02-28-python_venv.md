---
title: "python virtual environment"
date: '2025-02-28'
categories: [python, virtualenv]
tags: [python, venv, virtualenv, pyenv]
---

여러 프로젝트를 진행하다 보면 각 프로젝트마다 software dependency package가 다를 수 밖에 없다.  
특히 최근 인공지능 개발 열풍으로 파이썬이 많이 사용되면서, 각 프로젝트마다 dependency가 달라질 수 있다.

이에 따라 python version management와 virtual environment를 만들어야 하는 상황이 많이 생긴다.

다양한 방법이 있지만, 이 포스팅에서는 내가 가장 편하게 세팅했던 방법을 소개한다.

파이썬 virtual environment 세팅에서 가장 많이 사용되는 방법 중 하나는 **conda**이다.  
하지만 사내 정책상 conda의 사용이 제한되기 때문에(관련 내용은 [Anaconda Blog](https://www.anaconda.com/blog/sustaining-our-stewardship-of-the-open-source-data-science-community) 참조),  
여기서는 **pyenv**와 **venv**를 통한 개발 환경 세팅을 추천한다.

pyenv는 필요한 파이썬 버전을 설치할 수 있고,  
venv를 통해 해당 프로젝트에 필요한 패키지들을 설치하여 관리할 수 있다.

---

## Pyenv 설치 및 설정

### Step 1: 의존성 패키지 설치

```bash
sudo apt update
sudo apt install -y make build-essential libssl-dev zlib1g-dev \
    libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
    libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \
    libffi-dev liblzma-dev
```

### Step 2: pyenv 설치

pyenv를 설치하는 명령어는 다음과 같다.

```bash
curl https://pyenv.run | bash
```

설치 후, `pyenv`를 shell 설정 파일(예: `~/.bashrc`)에 추가한다.

```bash
export PATH="$HOME/.pyenv/bin:$PATH"
eval "$(pyenv init --path)"
eval "$(pyenv virtualenv-init -)"
```

변경 사항을 적용하려면 다음 명령어를 실행한다.

```bash
source ~/.bashrc
```

### Step 3: Python 설치 (예: 3.7 버전)

설치 가능한 파이썬 버전을 확인하려면:

```bash
pyenv install --list | grep 3.7
```

예를 들어, `3.7.12` 버전이 있다면 다음 명령어로 설치한다.

```bash
pyenv install 3.7.12
```

### Step 4: Python 3.7 버전 설정 (Global / Local)

Python 3.7을 전체 시스템에 적용하거나 특정 디렉토리에서만 적용할 수 있다.  
보통은 local 설정을 사용하는 것이 좋다.

- **글로벌 설정**

  ```bash
  pyenv global 3.7.12
  ```

- **로컬 설정**

  ```bash
  pyenv local 3.7.12
  ```

로컬 설정을 하면 해당 디렉토리에 `.python-version` 파일이 생성되어 Python 버전이 설정된 것을 확인할 수 있다.

### Step 5: venv를 사용한 Virtual Environment 생성

```bash
python -m venv venv
```

생성된 가상환경을 활성화하는 방법은 다음과 같다.

- **VSCode 사용 시:** 해당 디렉토리에서 VSCode를 실행하면 자동으로 가상환경을 인식한다.
- **터미널에서 직접 활성화**

  ```bash
  source venv/bin/activate
  ```

이제 이 환경에서 `pip` 등을 통해 필요한 패키지들을 설치하며 개발할 수 있다.

---

## References

- [Velog: pyenv와 pyenv-virtualenv 사용법](https://velog.io/@todaybow/pyenvpyenv-virtualenv)
- [Scshim's Tistory](https://scshim.tistory.com/602)