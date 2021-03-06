# DeepTto
- 파이썬, 텐서플로를 이용한 로또 분석
- 2021 - 02 - 21 기준


## 과정

### 1. 회차별 데이터 수집

- 라이브러리 : pandas, requests, BeautifulSoup
- 위의 라이브러리를 통해 1회차부터 가장 최근 회차(951회차)까지 회차별 데이터를 크롤링한다.


![과정1](https://user-images.githubusercontent.com/69676927/108626645-93e25e80-7494-11eb-8e2c-d974e3234dd5.jpg)


### 2. 원 핫 인코딩

- 로또는 1 ~ 45의 범위로 6가지 숫자가 주어진다. 이는 수치, 위치 상의 의미는 없는 범주형 데이터로 볼 수 있다.
- 따라서 원 핫 인코딩 처리를 해주어 45개 경우의 수 중 하나인 클래스로 다룬다.
- 만약 당첨번호가 "1,2,3,4,5,6"인 경우 [111111000000000000000000000000000000000000000] 이런 벡터로 표시할 수 있다.

![과정2](https://user-images.githubusercontent.com/69676927/108626654-ab214c00-7494-11eb-8652-75fc208e3591.jpg)


### 3. 데이터셋 만들기

- 수집한 데이터로 (X,Y)로 구성된 샘플을 구성한다. 당첨번호를 기준으로 1회차를 예로 들면(X = 1회차 당첨번호, Y = 2회차 당첨번호)의 구성
- 훈련(train)셋 : (1회 입력, 2회 출력) ~ (750회 입력, 751회 출력) 까지 750개 샘플
- 검증(validation)셋 : (751회 입력, 752회 출력) ~ (850회 입력, 851회 출력)까지 100개 샘플
- 시험(test)셋: (851회 입력, 852회 출력) ~ (950회 입력, 951회 출력)까지 100개 샘플

### 4. 모델 만들고 학습

- 텐서플로를 통해, 상태유지 LSTM 모델을 구성한다.
- 오버피팅이 일어나지 않은 최적의 모델을 찾기위해 매 에포크마다 학습된 모델 파일로 저장해준다. (로또는 독립시행이기에 깊은 의미는 없다..)
- 학습 후 그래프를 확인해본다. (역시 독립시행이기 때문에 validation set의 loss는 바로 올라간다.)

![과정3](https://user-images.githubusercontent.com/69676927/108626672-be341c00-7494-11eb-8949-080587816935.jpg)


### 5. 모델 검증

- 먼저 검증을 위한 metric으로 등수와 상금을 추가해준다.
- 이렇게 추가하는 것은 당첨 번호를 맞추는 것(1등)도 중요하지만, 몇 등인지 그리고 상금이 얼마인지도 중요하다. 라는 내용을 모델에게 알리는 것이다.
- 모델의 출력은 45개의 0.0 ~ 1.0 사이 값이 출력되는 1차원 벡터인데, 여기서 나온 값 하나로 로또를 5000원치 살 것은 아니므로 서로 다른 결과를 뽑을 수 있게 함수를 만든다.
- 그 후 모델을 검증, 750번까지는 이미 학습한 것이기에 믿을 수 없고, 그 후 학습이 로또는 학습으로 맞출 수 없다는 결과를 보여준다.


![과정4](https://user-images.githubusercontent.com/69676927/108626685-cab87480-7494-11eb-8fba-bceddba93c4a.jpg)

- train set, validation set, test set으로 상금을 막대그래프로 표시해보면 train set을 제외하고는 보이지도 않는다. ㅎ

![과정5](https://user-images.githubusercontent.com/69676927/108626697-db68ea80-7494-11eb-9fb8-2bbde37f8c2e.jpg)


### 6. 마지막 회차까지 학습한 모델로 다음 회차 추론

- 다음회 로또 번호를 예측하기 위해서는 직전까지만 학습한 것을 가지고 다음 회차만 추론하면 된다.
- 한 스텝을 예측하는 문제는 walk-forward model validation 방법을 사용한다.
- walk-forward model validation을 이용한 검증 및 검증 셋을 이용한 하아피파라미터 튜닝을 해야하지만 로또 번호는 학습이 큰 의미가 없음을 이미 어느정도 느꼈으므로 다음 기회에..

![과정6](https://user-images.githubusercontent.com/69676927/108626727-03f0e480-7495-11eb-8c11-15f59a7b745d.jpg)

