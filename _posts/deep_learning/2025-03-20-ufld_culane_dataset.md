---
title : CULane Dataset을 활용한 UFLD Dataset과 Dataloader 구성하기
date : '2025-03-20'
categories : [Deep Learning]
tags : [culane, ufld, lane detection, dataset, dataloader, deep learning]
image: assets/img/culane06.png 
---

이 페이지에서는 **UFLD (Ultra Fast Lane Detection)**와 함께 **CULane Dataset**을 어떻게 활용하는지 살펴보겠다. 
처음엔 어렵게 느껴질 수 있어도 어려운게 아니라 익숙하지 않아서 그러니, 여러번 해보고 익숙해지는 것을 목표로 지속적으로 학습해보자!

포스트 마지막에는 간단한 문제를 통해 복습할 수 있도록 해놓았다. 계속 보면서 익숙해져보자!

> ![Forget Not](assets/img/forget.jpg)  
> *문제 풀이를 통해 잊지 않고 꾸준히 복습하도록 하자.*

---

## UFLD & CULane Dataset 소개

UFLD는 lane detection 네트워크 중 하나로, 기존의 segmentation 방식이 아닌 **anchor-based** 방식으로 결과를 도출하도록 설계되었다. 본 포스트에서는 특히 **CULane Dataset**을 활용하여 UFLD의 학습 데이터를 준비하는 방법을 다룬다. 특히 segmentation 데이터셋을 anchor 기반 lane detection에 어떻게 활용할 수 있는지 알아본다.

---

## CULane Dataset Investigation

먼저, CULane Dataset에 대해 간략히 살펴보자.

- **데이터 출처:**  
  본 데이터셋은 Beijing에서 다양한 운전자들이 운전하는 6대의 차량을 촬영한 데이터로 구성되어 있다. 자세한 설명은 [공식 문서](https://xingangpan.github.io/projects/CULane.html)를 참고할 수 있다.

- **폴더 구조:**  
  데이터를 압축 해제하면 5개의 driving dataset이 확인되며, 이 중 3개는 **Training & Validation** set, 나머지 3개는 **Test** set으로 구성되어 있다.  
  예를 들어,  
  - Training & Validation set:  
    - `driver_23_30frame.tar.gz`  
    - `driver_161_90frame.tar.gz`  
    - `driver_182_30frame.tar.gz`
  - Test set:  
    - `driver_37_30frame.tar.gz`  
    - `driver_100_30frame.tar.gz`  
    - `driver_193_90frame.tar.gz`

  > ![Dataset Folder Structure](assets/img/culane02.png)  
  > *(위 이미지는 CULane Dataset의 폴더 구조를 보여준다.)*

- **Annotation 방식:**  
  CULane은 **cubic spline**을 이용하여 lane marking을 annotation한다.  
  중요한 점은 segmentation 방식이 아닌 spline을 활용해 annotation을 진행한다는 것이다. segmentation 이미지도 있지만 이것은 spline을 활용해 가공해서 만든 GT인 것이다.
  만약 lane marking이 차량에 의해 가려지거나 명확하지 않더라도, 주변 context를 참고하여 annotation을 수행한다. 따라서 이를 활용한 모델도 lane prediction을 통해 예측이 가능하게 된다. 사실 segmentation based lane detection은 이렇게 가려진 부분에 대해서 예측하는 것이 어렵지만 UFLD는 lane shape에 대한 loss function이 있기 때문에 occlusion등의 가려진 부분도 예측이 가능해지는 것이다.

- **파일 구성:**  
  각 폴더 내에는 영상의 프레임을 나타내는 jpg 파일과 해당 annotation 정보가 담긴 txt 파일이 존재한다.  
  이미지의 크기는 1640 x 590으로, 옆으로 넓은 형태이다.

  > ![Image & Annotation Sample](assets/img/culane03.png)  
  > *각 이미지와 txt 파일이 어떻게 대응되는지 확인할 수 있다.*

- **Annotation Details:**  
  txt 파일에는 최대 4개의 lane marking에 대해, 각 lane마다 여러 개의 **line sampling point** (x, y 좌표)가 기록되어 있다.  
  예를 들어, 아래와 같이 값들이 나열된다.

  ```python
  240.573 590 257.848 580 275.127 570 292.409 560 ... 778.228 290
  ```

  이 값들은 이미지 상에서 아래에서 위로, 즉 y 값이 10픽셀씩 감소하는 방식으로 sampling되며, 일부 negative나 out-of-bound 값은 lane을 extend하는 과정에서 발생한다.  
  다만 UFLD에서는 이 feature points를 직접 학습에 사용하지 않고, segmentation ground truth를 가공하여 anchor ground truth를 생성한 후 학습에 활용한다.

- **Additional Annotation Info:**  
  각 이미지에 대응되는 **_gt.txt** 파일을 확인하면, 이미지 경로, segmentation label 경로, 그리고 네 개의 binary flag (l0–l3)를 통해 각 lane marking의 존재 여부를 확인할 수 있다.  
  예를 들어,

  ```python
  <image_path> <segmentation_label_path> 1 1 1 1
  ```

  이와 같이 l0부터 l3까지 각각 1(존재) 또는 0(부재)로 표현된다. 자세한 내용은 [관련 문서](https://github.com/voldemortX/pytorch-auto-drive/blob/master/docs/datasets/CULANE.md)를 참고한다.

- **Test Split:**  
  test_split 폴더 내에는 9개의 카테고리로 구분된 test 데이터셋 리스트가 존재한다.  
  이는 나중에 모델 성능 평가 시, 다양한 상황에서 얼마나 정확하게 detection하는지 확인하기 위한 중요한 metric으로 사용된다.

  > ![Test Split Categories](assets/img/culane05.png)  
  > *Test split에 포함된 9개의 카테고리 예시*

- **Visualization:**  
  이미지와 point-wise annotation을 중첩하여 확인하면 아래와 같은 결과를 얻을 수 있다.

  > ![Overlapped Annotation 1](assets/img/culane06.png)  
  > ![Overlapped Annotation 2](assets/img/culane07.png)

<details>
  <summary> visualization code </summary>
  <div markdown="block">
``` python 
# %%
import os
import cv2
import numpy as np
import matplotlib.pyplot as plt

# %%
DIR = "./culane/driver_161_90frame/06030819_0755.MP4/"
img = plt.imread(DIR+"00000.jpg")
plt.figure(figsize=(16,18))
plt.imshow(img)

# %%
!cat "./culane/driver_161_90frame/06030819_0755.MP4/00000.lines.txt"

# %%
exist_list = []
listfile = DIR + "00000.lines.txt"
with open(listfile) as f:
    for line in f:
        line = line.strip()
        l = line.split(" ")
        exist_list.append([int(eval(x)) for x in l[2:]])

# %%
exist_list[0][0],exist_list[0][1]

# %%
img = img.copy()  # 이미지 배열을 writable하게 복사

for j in range(len(exist_list)):
    for i in range(0, len(exist_list[j]), 2):
        cv2.circle(img, (exist_list[j][i], exist_list[j][i+1]), radius=0, color=(0, 255, 0), thickness=10)


# %%
plt.figure(figsize=(16,18))
plt.imshow(img)

# %%
mask = plt.imread("./culane/laneseg_label_w16/driver_161_90frame/06030819_0755.MP4/00000.png")
plt.figure(figsize=(16,18))
plt.imshow(mask*255);

# %%
np.unique(mask), np.unique(mask*255)

# %%

```
</div>
</details> 
