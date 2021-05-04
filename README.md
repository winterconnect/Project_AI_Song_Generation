# AI Beatles
#### 누구나 작곡가, 작사가가 될 수 있다! 



## 1. 배경
- AI가 사람처럼 노래하고, 노래를 만드는 시대
- 하지만 창작은 아직까지 많은 에너지와 시간이 필요한 예술활동이다.
- AI로 **곡과 가사를 만들어** 창작자에게 많은 샘플을 제공함으로써 창작활동에 도움을 줄 수 있다.


## 2. 프로젝트 활용방안

### 1) AI 작곡/작사 서포터
- 스튜디오로부터 곡과 가사에 대한 데이터를 제공받아 가사와 멜로디를 만들어 제공하면, 스튜디오는 **곡을 사용**하거나 **알맞게 수정**하여 상업음악으로 만들어낼 수 있다.
- 사람이 직접 작사/작곡한 곡에 비해 바로 사용할 수준은 아니라고 하더라도, 샘플을 만드는 데 필요한 **인건비와 시간**을 고려하면 경쟁력이 있다.

### 2) AI 싱어송라이터 플랫폼(상업성/공공성)
- 곡이나 가사를 변형하거나, 새롭게 생성해주는 플랫폼을 개발하여 이용자는 마음에 드는 곡을 바로 고르거나 창작의 영감을 얻을 수 있다.
- 전문기술이 필요해서 어렵게 느껴지는 작곡과 작사의 진입장벽을 낮춰 **누구나 원한다면 작곡가, 작사가가 되는 즐거움을 느낄 수 있다.**


## 3. 진행과정

<img src = "https://github.com/winterconnect/Project_AI_Song_Generation/blob/main/img/flowmap.png?raw=true">

### 1) Web Crawling
- 가사가 아름다운 발라드 1300여곡을 수집

### 2) Data Preprocessing
- 결측치 제거 후 정규식으로 특수문자, 숫자, 영어 제거
- Tokenization: KoNLPy의 Okt 형태소분석기로 토큰화
- 음악정보(라흐마니노프 피아노곡)를 임베딩 후 Note Index 생성
- 가사정보(발라드 1300곡)를 임베딩 후 Word Index 생성

### 3) Modeling
- TextRank
  - Note Index-Word Index 간 변환을 통해 라흐마니노프의 피아노곡을 문장으로 변환
  - 전체 문장 466개 중 10개의 핵심문장 추출
- TF-IDF
  - 핵심 10문장에서 핵심 키워드 추출
- LSTM
  - 키워드에서 이어지는 가사 한줄을 생성(후렴으로 활용)
- SeqGAN
  - 키워드(TF-IDF) 및 후렴구(LSTM)를 활용하여 Generator로부터 새로운 가사 생성
- Transformer
  - 후렴구에 이어지는 4줄의 가사 생성(노래의 한 절로 활용)
- 최종가사 생성: 절(Transformer) + 후렴구(LSTM)


## 4. Model Architecture

### 1) LSTM

<img src ="https://github.com/winterconnect/Project_AI_Song_Generation/blob/main/img/LSTM.png?raw=true">

- input: 가사 한줄(총 112,336개)
- output_dim: 128
- LSTM(256)
- Parameters: 8,679,825 
- 40s per epoch, 총 200 epochs(약 2시간 소요)


### 2) SeqGAN

<img src = "https://github.com/winterconnect/Project_AI_Song_Generation/blob/main/img/SeqGAN.png?raw=true">

- Pre-train Generator(10회~ 120회)
- Pre-train Discriminator(5회~50회)
- Adversarial Training (Generator vs. Discriminator): 평균 150회
- 모델별로 소요시간은 상이하나, G-D 밸런스가 맞게 학습시키기까지 약 4일 소요


### 3) Transformer

<img src = "https://github.com/winterconnect/Project_AI_Song_Generation/blob/main/img/Transformer.png?raw=true">

- Parameters: 46,856,113
- 4mins per epoch (약 1일 소요)



## 4. 결과

<img src ="https://github.com/winterconnect/Project_AI_Song_Generation/blob/main/img/output.png?raw=true">

- 프로젝트 최종 발표 시 "AI vs. Human" 이라는 작은 코너를 만들어 우리 모델이 만든 가사와 진짜 사람이 만든 가사를 함께 두고, 진짜 사람이 만든 한 곡을 투표하도록 했다.
- 세 곡 중 2개는 우리의 모델이, 하나는 사람이 만든 곡이었다.
- (시간을 많이 주지 않은 덕도 있겠지만) 생각보다 표가 골고루 나왔고, 정말 헷갈렸다는 의견도 많았다.
- 인공지능을 통한 창작과 생성모델의 가능성을 본 것 같아 설레고 뿌듯했다.


## 5. 프로젝트를 하며 느낀 점

### 1) 팀워크의 중요성
- 어떤 프로젝트이든, 내용도 중요하지만 얼마나 협업하는지가 정말 중요하다고 느낀 프로젝트였다.
- 교육과정동안 배운 것보다 어려운 내용의 모델들(특히 SeqGAN, Transformer 등)이 많아 구현하는 데 몇번이나 어려움이 있었고, 심적으로 부담이 왔다.
- 그럴 때마다 어려운 부분을 허심탄회하게 털어놓고 부족한 부분을 메워주는 팀원들이 있어서 프로젝트를 잘 마무리할 수 있었다. 끝난 후 돌아보니 인간적으로도, 지적으로도 정말 많은 것을 배운 프로젝트였다.

### 2) 사용하는 모델에 대한 이해
- 코드와 오픈 API는 구하기가 정말 쉽다. 하지만 단순히 복사해서 "어쨌든 돌아만 가도록" 구현하는 것과, 지금 내가 쓰는 모델이 어떤 원리로 동작하고 어떤 장단점이 있는지를 이해하고 쓰는 것은 정말 큰 차이가 있음을 느꼈다.
- 특히 생소한 SeqGAN 모델을 구현하면서 그것을 많이 느꼈다. 사실 코드는 조금만 검색을 해봐도 참고할 수 있을만한 여러가지 버전의 코드들이 골라서 쓸 수 있을만큼 많았다.
- 하지만 코드만 보아서는 그것이 어떻게 동작하는지, 그리고 오류가 있을 때에는 어떻게 대처해야하는지 알 수 없었다. 결론적으로 SeqGAN을 처음 소개한 논문(SeqGAN: Sequence Generative Adversarial Nets with Policy Gradient)과 관련된 여러 논문들을 읽은 후에야 모델에 대해 이해할 수 있었고, 구현 또한 속도가 나게 되었다.
- 이후 트랜스포머 모델도 "Attention Is All You Need" 논문을 읽으면서 모델에 대해 더 이해할 수 있게 되었다. 
- **결론: 이해하고 써야 코딩이 즐겁다. 논문을 (즐겁게) 읽자.**


## 6. References
- https://github.com/MrSyee/pokemon_story_generator
- https://github.com/NLP-kr/tensorflow-ml-nlp-tf2
- https://github.com/ZiJianZhao/SeqGAN-PyTorch
- https://wikidocs.net/85470
- SeqGAN: Sequence Generative Adversarial Nets with Policy Gradient
- Attention Is All You Need

