# Lane Detection with OpenCV

### OpenCV를 이용한 차선 인식
| OpenCV를 이용한 영상 처리 및 차선 인식<br>
| 차선에서 벗어난 정도 detecting<br>
| 현재 핸들 상태 plotting

---

### Process of lane detection

- 원본 이미지

<center>

![road1](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/6ecacce5-1889-439b-a8b4-396c96fa486b)
</center>

- Canny Edge Detection
  - 불필요한 잡음(noise)를 제거하기 위해 Canny Edge Detection을 수행
  - OpenCV의 Canny() 메소드를 이용해 threshold를 조정하여 수행

<center>

![canny_img](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/f60ab9a7-0bda-4e65-8878-ebf68ba55f33)
</center>

- ROI 영역 지정
  - Region of Interest
    - 관심 영역
    - 필터링하거나 연산을 수행하려는 영상의 부분
  - 주변 객체들을 제외하고 차선만을 detect하기 위해 차선 영역만을 ROI로 지정
  - numpy의 np.zeros() 메소드를 이용해 mask를 생성
  - OpenCV의 fillPoly() 메소드를 이용해 이미지에서 mask 영역 반환

<center>

![roi_img](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/02b2a0fb-5a36-4e8a-a01f-2fdd94f80a36)
</center>

- Perspective Transform
  - 원근 변환, 투시 변환
    - 영상의 기하학적 변환 중 어파인 변환(Affine Transform)보다 자유도가 높음
    - 직사각형 형태의 영상을 임의의 블록 사각형 형태로 변환
    - 왜곡된 영상을 펴는데도 사용
  - ROI 이미지에 Perspective Transform을 적용
  - Bird Eye View 형태로 변환
    - 조감도, 전경, 개관, 대요
    - 새가 하늘에서 내려다보듯 땅의 기복을 표현한 지도 혹은 그림
  - OpenCV의 getPerspective Transform() 메소드와 warpPerspective() 메소드를 이용해 변환 matrix와 변환 이미지 반환
  - 이때 변환 이후 이미지의 크기는 동일하되, 차선이 곧게 될 수 있도록 변환 matrix 조정 필요
  
<center>

![perspective_transform_img](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/7c9593da-1ace-433c-a328-89c7b9d2586f)
</center>

- lane detection
  - Perspective Transform을 수행한 이미지에 Sliding Window Search를 수행하여 차선 부분을 검출
  - 이미지의 히스토그램을 계산해 Window가 탐색을 시작할 위치를 설정하고, <br>for문으로 탐색을 반복 수행하여 차선 영역을 검출
  - parameters
    - 윈도우의 개수, 윈도우의 크기, 윈도우 내에 검출되어야 하는 픽셀의 개수, 픽셀의 임계값
  - Sliding Window Search를 통해 차선을 검출했다면 해당 영역을 표시
    - ROI 영역 내에 검출 결과가 담긴 array를 참조하여 해당 영역을 표시

<center>

![sliding_window_img](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/240703f1-7b05-4a24-bf07-d9afa596da92)

![result](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/d0d6f413-fbcf-4029-b971-b5e973285eb6)
</center>

---

### Additional Features

<center>

![additional_features](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/df211620-54cc-407f-8008-93ba4dbe3789)
</center>

- offset
  - 차량이 차선 중앙으로부터 벗어난 정도를 표시
  - offset은 이미지 맨 아래 중간 지점과 차선 맨 아래 중간 지점 간의 차이
  - 일반적인 차선 폭은 3m, 검출한 차선의 맨 아래 양쪽 px의 차이와 계산했을 때 한 px 당 0.5cm
  - Bird Eye View에서 offset 영역을 표시하고, warpPerspective() 메소드를 이용해 offset을 표시
  - 만약 offset이 15cm, 30px을 넘어가면 'CAUTION' 표시
- Steering wheel
  - 핸들을 돌리는 각도와 차량의 회전 각도는 약 12:1 ~ 20:1의 비율
  - 핸들의 회전을 눈에 띄게 하기 위해 20:1로 구현
  - 차선 영역의 맨 아래 중간 지점과 맨 위 중간 지점, 맨 위 중간 지점에서 이미지 맨 아래로 수선의 발을 내린 각도로 핸들의 회전 각 계산
  - OpenCV의 getRotationMatrix2D() 메소드, warpAffine() 메소드를 이용해 현재 핸들의 상태 표시
  
---

### 동작

- 원본 영상
<center>

![drive](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/9b1c5e82-96ca-4503-a818-e09c10d94684)
</center>

- 차선 인식 결과
<center>

![result](https://github.com/GreenClothes/Lane-detection-with-OpenCV/assets/106455871/19d86ef7-b971-40c4-b1de-07d0549b0be9)
</center>