---
title: "Understanding Git Submodules and Common Issues"
date : '2025-02-21'
categories: [git]
tags: [git, submodule]
---

## Git Submodules

Git Submodule은 하나의 repository에서 여러 개의 repository를 함께 관리할 수 있도록 해주는 기능이다. 
이를 통해 여러 관련 repository를 한 곳에서 관리할 수 있으며, 개별적으로 clone하지 않고도 접근 가능하다.

Git Submodule은 여러 장점이 있지만, 개인적으로는 관련된 repository를 한 repo에서 다룰 수 있다는 점에서 유용하게 사용하고 있다. 
많은 개발자들도 이러한 이유로 Submodule을 활용하고 있을 것이라 생각된다.

하지만 새로운 Submodule을 추가하거나 기존 Submodule을 업데이트할 때, 여러 가지 문제가 발생할 수 있다. 
예를 들면, Submodule 디렉터리가 비어 있는 경우가 있다. 이러한 문제를 자주 경험한 후, 이를 정리하여 기록하는 것이 좋겠다 생각하여 이번에 정리하게 되었다.

## Repository Clone including Submodule

Submodule이 포함된 repository를 clone할 때는, 처음부터 Submodule까지 가져오면 좋다. 아래 명령어를 사용하면 됨:

```bash
git clone --recurse-submodules <repo_url>
```

이렇게 하면, repository와 함께 Submodule의 내용도 함께 clone됨.

만약 일반적인 `git clone`을 실행했다면, Submodule이 빈 디렉터리로 남아있을 가능성이 있다. 이때, 수동으로 Submodule을 초기화하고 업데이트해야 한다:

```bash
git submodule init
git submodule update
```

혹은 한 줄로 실행할 수도 있음:

```bash
git submodule update --init --recursive
```

### `--init` 및 `--recursive` 옵션 설명
- `--init`: `.gitmodules` 파일을 읽어 Submodule 정보를 `.git/config`에 등록함.
- `--recursive`: Submodule 내부에 또 다른 Submodule이 있을 경우, 그것까지 함께 초기화하고 업데이트함.

## `.gitmodules` 및 `.git/config` 이해하기

`.gitmodules` 파일은 Submodule의 설정 정보를 저장함. 각 Submodule의 경로, URL, branch 등이 포함됨:

```ini
[submodule "deep_learning_practice"]
	path = deep_learning_practice
	url = https://github.com/JO-Gaehwan/deep_learning_practice
	branch = main

[submodule "blog"]
	path = blog
	url = git@github.com:JO-Gaehwan/JO-Gaehwan.github.io.git
	branch = main
```

`git submodule init`을 실행하면 위 정보가 `.git/config`에 등록되지만, 실제 데이터를 가져오지는 않음. 데이터를 가져오려면 `git submodule update`를 실행해야 함.

## 원치 않는 Submodule 제거하기

가끔 의도하지 않게 Submodule이 추가되는 경우가 있다. 이런 경우 다음 단계를 따라 Submodule을 완전히 제거해야 한다.

### **불필요한 Submodule 삭제 방법**

1. **Submodule 비활성화 (`.git/config`에서 제거)**
   ```bash
   git submodule deinit -f -- <submodule_path>
   ```

2. **`.git/modules/`에서 Submodule 데이터 삭제**
   ```bash
   rm -rf .git/modules/<submodule_path>
   ```

3. **`.gitmodules`에서 Submodule 정보 삭제**
   ```bash
   vim .gitmodules  # 수동으로 Submodule 항목 제거 하라
   ```

4. **실제 Submodule 폴더 삭제 (Optional)**
   ```bash
   rm -rf <submodule_path>
   ```

이렇게하면 Submodule이 완전히 제거될 것이다.

```bash
git submodule update --init --recursive
```