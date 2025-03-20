---
title: "Python Package 설치 및 이용 방법"
date: '2025-03-20'
categories: [python, package]
tags: [python, package]
---

이 페이지에서는 **Python Package**를 만드는 이유와 그 방법에 대해 살펴보겠다. 처음에는 개념이 복잡해 보일 수 있으나, 올바른 패키지 구조를 이해하고 적용하면 프로젝트 관리, 모듈 임포트, 그리고 코드 재사용 측면에서 엄청난 이점을 얻을 수 있다. 

### 왜 Python Package로 설치해야 할까?

- **모듈 경로 문제 해결:**  
    import 관련 에러중에  
    ```python
    ModuleNotFoundError: No module named 'xxx'
    ```
    를 자주 볼 수 있을 것이다. 
    
    이는 설치된 파이썬 경로를 기준으로 package를 찾기 때문인데,
  패키지로 설치하면, 파이썬은 패키지를 시스템의 site-packages 내에 등록된 모듈로 인식한다. 즉, 프로젝트 루트의 경로를 PYTHONPATH에 매번 추가할 필요 없이, 
  ```bash
  pip install -e .
  ```
  를 통해 패키지를 설치하면 자동으로 올바른 경로가 설정된다. 이렇게 하면  
  ```bash
  python -m data.dataset
  ```
  와 같이 모듈 단위로 실행하거나, 다른 스크립트에서  
  ```python
  from data.mytransforms import find_start_pos
  ```
  와 같이 임포트해도 문제없이 작동하게 된다.

- **개발 생산성 향상:**  
  editable mode (`pip install -e .`)로 설치하면, 설치된 패키지가 가상환경 내의 site-packages에 심볼릭 링크(egg-link 파일)로 등록된다. 이로 인해 소스 코드를 수정할 때마다 매번 재설치할 필요 없이 즉시 반영되어 개발 속도가 빨라진다.

- **프로젝트 관리 및 배포의 용이성:**  
  올바른 패키지 구조를 갖추면, 팀원들과의 협업 및 향후 다른 프로젝트로의 재사용이 훨씬 수월해진다. 또한, 추후 PyPI 등 공개 저장소에 배포할 때도 표준화된 구조 덕분에 관리가 용이하다.

이처럼, Python Package를 구성하고 한 번 설치해두면, 모듈 임포트 문제를 비롯한 다양한 번거로움이 해소되어 코드의 유지보수와 재사용성이 크게 향상된다.

---

## Python Package란?

**Python Package**란 여러 모듈(Module)과 서브패키지(Subpackage)들을 하나의 디렉터리 구조로 묶어, 재사용성과 유지보수성을 높이기 위해 만든 집합체이다.  
- **Module:** 함수, 클래스, 변수 등이 정의된 하나의 `.py` 파일  
- **Package:** 여러 모듈을 포함하는 디렉터리로, 보통 `__init__.py` 파일을 포함하여 파이썬이 패키지로 인식하게 한다.

---

## 왜 Python Package를 만드는가?

Python Package를 만드는 이유는 다음과 같다:

- **코드 관리의 효율성 (Maintainability):**  
  여러 기능들을 관련된 모듈들로 분리해 관리할 수 있으므로, 프로젝트가 커져도 유지보수가 용이하다.

- **재사용성 (Reusability):**  
  패키지 형태로 만들어두면 다른 프로젝트에서도 손쉽게 설치하고 사용할 수 있다.

- **Namespace 관리:**  
  서로 다른 모듈 간의 이름 충돌을 방지하고, 명확한 계층 구조로 코드를 구성할 수 있다.

- **개발 환경 개선 (Development Workflow):**  
  `pip install -e .`와 같이 editable mode로 설치하면, 소스 코드를 수정할 때마다 재설치할 필요 없이 바로 반영되어 개발 속도가 빨라진다.

---

## Python Package 구성 방법

Python Package는 보통 다음과 같은 구조로 구성된다:

```
ufld/                      # 프로젝트 루트
├── package_name/          # 실제 패키지 디렉터리 (예: ufld)
│   ├── __init__.py        # 패키지 초기화 파일 (빈 파일이어도 됨)
│   ├── module1.py         # 개별 모듈 파일
│   └── subpackage/        # 서브패키지 디렉터리
│       ├── __init__.py    
│       └── module2.py
├── setup.py               # 패키지 설치를 위한 설정 파일
└── README.md              # 프로젝트 설명 파일
```

### 주요 파일 설명

- **`__init__.py`:**  
  해당 디렉터리를 Python Package로 인식하게 하는 역할을 한다. 이 파일 안에 초기화 코드나 패키지에서 외부로 공개할 내용을 정의할 수도 있다.

- **`setup.py`:**  
  setuptools를 사용해 패키지의 메타데이터(이름, 버전, 의존성 등)를 설정하고, 설치 과정을 정의한다.  
  예시:
  ```python
  from setuptools import setup, find_packages

  setup(
      name='ufld',                # 패키지 이름
      version='0.1.0',            # 버전 정보
      packages=find_packages(),   # 모든 패키지를 자동으로 찾아 포함
      install_requires=[          # 필요한 의존성 목록
          'numpy',
          'torch',
          'opencv-python',
      ],
      author='Your Name',
      author_email='your.email@example.com',
      description='A lane detection project',
  )
  ```
  이 파일은 **local installation** 및 개발 환경에서의 사용에 매우 유용하다.

---

## Python Package 설치 및 이용 방법

패키지를 만들고 나면, **editable mode**로 설치하여 개발 중에도 변경 사항이 바로 반영되도록 할 수 있다.

### 설치 방법

1. 터미널에서 프로젝트 루트 디렉터리(예: `ufld/`)로 이동한다.
2. 아래 명령어를 실행하여 editable mode로 설치한다:
   ```bash
   pip install -e .
   ```
   이 방식은 가상환경(venv)의 `site-packages`에 심볼릭 링크(egg-link 파일)를 생성하여, 소스 코드를 수정할 때마다 바로 반영되도록 한다.

### 이용 방법

설치 후에는, 다른 파이썬 스크립트나 Jupyter Notebook에서 쉽게 패키지 모듈을 import하여 사용할 수 있다.
예를 들어:
```python
from package_name.module1 import some_function

some_function()
```
이렇게 하면, 패키지 내부의 함수나 클래스를 자유롭게 활용할 수 있다.

---

## 간단한 예시

아래는 간단한 Python Package를 구성하는 예시이다.

1. **디렉터리 구조:**
   ```
   my_package/
   ├── my_package/
   │   ├── __init__.py
   │   └── math_utils.py
   └── setup.py
   ```

2. **`math_utils.py` 내용:**
   ```python
   def add(a, b):
       return a + b

   def subtract(a, b):
       return a - b
   ```

3. **`__init__.py` 내용:**  
   (빈 파일 또는 필요한 초기화 코드를 포함)

4. **`setup.py` 내용:**
   ```python
   from setuptools import setup, find_packages

   setup(
       name='my_package',
       version='0.1',
       packages=find_packages(),
   )
   ```

5. **설치 및 사용:**
   ```bash
   pip install -e .
   ```
   그리고, 다른 스크립트에서:
   ```python
   from my_package.math_utils import add, subtract

   print(add(3, 4))  # 7 출력
   print(subtract(10, 5))  # 5 출력
   ```

이처럼 Python Package를 만들면, 코드 관리가 용이해지고 다른 프로젝트에서도 재사용할 수 있는 모듈을 손쉽게 구성할 수 있다.

---

## 문제 풀이를 통한 복습

아래 문제들을 풀어보며, Python Package의 개념과 구성을 확실히 이해해보자.

**문제 1:** Python Package를 구성할 때 반드시 필요한 파일은 무엇인가?  
**문제 2:** editable mode로 설치하면 어떤 장점이 있는가?  
**문제 3:** `setup.py` 파일에서 `find_packages()`의 역할은 무엇인가?

<details>
  <summary>문제 풀이 답안 보기</summary>
  <div markdown="block">
  
  **문제 1 답:**  
  - **필수 파일:** `__init__.py`  
    → 이 파일은 해당 디렉터리를 Python Package로 인식시키는 역할을 한다.
  
  **문제 2 답:**  
  - **editable mode의 장점:**  
    코드 변경사항이 바로 반영된다.  
    → 패키지가 가상환경의 site-packages에 심볼릭 링크(egg-link 파일)로 등록되어, 소스 코드를 수정할 때마다 재설치할 필요 없이 바로 업데이트된다.
  
  **문제 3 답:**  
  - **`find_packages()`의 역할:**  
    프로젝트 디렉터리 내의 모든 서브 패키지(즉, `__init__.py`가 존재하는 폴더)를 자동으로 찾아내어 패키지 목록에 포함시킨다.
  
  </div>
</details>

---

문제 풀이를 통해 잊지 않고 꾸준히 복습하도록 하자. 계속 보면서 익숙해지고, 직접 Python Package를 만들어보며 실습해보자!