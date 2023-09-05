## MIMIC DATA

### MIMIC3 DATASET

MIMIC3 데이터는 `EMR(Electric Medical Record)`로 2001년과 2012년 사이에 Beth Isreal Deaconess Medical Center 중환자실에 입원한 4만 명 이상의 환자와 관련된 비식별화된 건강 관련 데이터이다. MIMIC3 는 다음과 같은 정보를 제공하고, 

1. 중환자 정보 시스템
2. 병원 전자 건강 기록 데이터베이스
3. 병원 밖 사망에 대한 기록

데이터 사용 규약에 대한 교육을 듣고 시험을 통과하면 데이터에 접근이 가능하다. 데이터에 대한 통계를 시각화하여 데이터를 살펴보면 다음과 같다.

<img width="698" alt="image" src="https://github.com/ranunclulus/Software-Capston/assets/87214089/101a2c63-b15c-4dd5-b525-1e28548c9537">


## MedCAT Library

### MedCAT Library

MedCAT Library는 비식별화된 EMR 데이터를 대형 서버 없이도 개인 컴퓨터에서 대형 병원의 EMR DATA로 구조화할 수 있게 도와주는 라이브러리이다. `NER (Named Entity Recognition)` + `L (Linking)` 단계를 거쳐 작업이 수행되어진다. 특정 분야에 해당하는 전문 용어에 대한 개체명 인식이 필요하기 때문에, `UMLS (Unified Medical Language System)` 의료 데이터 베이스도 활용해서 진행한다.

### Vocab CDB

먼저 MedCAT에 환자들의 상태에 대한 문자 기록을 입력하면, MedCAT은 Vocab 모델을 통해 철자 검사 및 단어 임베딩을 진행한다. 임베딩 방법은 Word2VEC, BERT 등 원하는 방법을 사용할 수 있다. 하지만 이 프로젝트에서는 BERT를 사용했다. 단어에 대한 임베딩이 끝나면 다음과 같은 모습이 된다.

<img width="698" alt="image" src="https://github.com/ranunclulus/Software-Capston/assets/87214089/0d827e42-fa7f-4899-a4a7-f48c4fdddcbb">


이후 MedCAT를 사용할 때 필요한 두 번째 모델인 Concept Database (CDB)를 구축해야 한다. 이 데이터베이스는 텍스트에서 감지하고 연결하려는 모든 의학적 개념의 목록을 포함한다. 의학 응용 프로그램에서는 주로 UMLS 또는 SNOMED CT와 같은 대규모 데이터베이스를 사용하지만 MedCAT는 어떤 크기의 데이터베이스든 사용할 수 있다. 이 CDB 데이터베이스는 다음과 같은 형태로 가공된다.

<img width="709" alt="image" src="https://github.com/ranunclulus/Software-Capston/assets/87214089/68ed9cf3-44fb-4315-800c-c7e3867b4ece">


```arduino
cui,name,ontologies,name_status,type_ids,description
1,Kidney Failure,SNOMED,P,T047,kidneys stop working
```

나머지 필드는 선택 사항이며 CSV에 포함하거나 생략할 수 있다.

- **`ontologies`** - 출처 온톨로지이다. 예: HPO, SNOMED, HPC,...
- **`name_status`** - 용어 유형, 예: P - 기본 이름.
- **`type_ids`** - 유형 ID는 개념이 속할 수 있는 넓은 범주다. 이는 특정 범주에 속하는 개념을 빠르게 필터링하는 데 사용된다.
    - UMLS의 경우 Semantic type 식별자 (예: T047)
    - SNOMED의 경우 Semantic 태그 (예: (Disease))
- **`description`** - 이 개념에 대한 설명.

### NER + L

이후 `Named Entity Recognition with Linking`을 진행한다. Vocab과 CDB를 파이프라인으로 진행하는 Unsupervised Learning이다. 총 90만 개의 데이터로 학습했다. 이후 모델 학습이 끝나고 실행시키면, Detection 된 Entity들 중에 UMLS 의학 데이터베이스에 연결되는 키워드만 추출해서 유의미한 단어만 거를 수 있다. 이렇게 생물 의학 데이터베이스에 연결하면 해당 데이터베이스의 모든 구조화된 필드에 접근이 가능해진다.
<img width="706" alt="image" src="https://github.com/ranunclulus/Software-Capston/assets/87214089/1ff5a2ed-b8e1-4dbf-a133-07676e72e0f0">
