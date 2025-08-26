## 원본 데이터 Input Data

```
# RAG 실전 가이드
작성자: 손용훈 | 버전: v1.2 | 업데이트: 2025-08-26

## 1. RAG란?
RAG(Retrieval-Augmented Generation)는 외부 지식 검색 결과를 LLM 입력에 주입하여, 최신성과 사실성을 강화하는 아키텍처입니다.

### 1.1 왜 필요한가
- 최신성: 모델이 학습 이후의 정보를 모름
- 정확성: 환각(Hallucination) 감소
- 근거: 출처 링크 제공 가능

## 2. 파이프라인 구성
1) 문서 수집 → 2) 텍스트 분할 → 3) 임베딩 → 4) 벡터DB 적재 → 5) 질의 시 검색 → 6) 프롬프트 구성 → 7) 생성

### 2.1 텍스트 분할 전략
- Character: 간단하지만 문맥 파손 위험
- RecursiveCharacter: 문맥 보존 우수(권장)
- MarkdownHeader: 문서 구조 반영
- Code/HTML/JSON 전용 스플리터: 포맷 맞춤형

## 3. 프롬프트 설계
**지시문 + 컨텍스트 + 제약조건 + 출력형식**의 4요소를 명시합니다.
예) "아래 컨텍스트만을 근거로 한국어로 5문장 요약하세요. 근거 문서 제목과 헤더를 함께 표기하세요."

## 4. 품질 측정
- 정답성(groundedness)
- 인용 정확도(citation precision)
- 재현성(reproducibility)
- 속도/비용

## 5. 운영 팁
- 쿼리 리라이팅(Query Rewriting)
- 하이브리드 검색(BM25 + 벡터)
- 다단계 RAG(Routing / Re-Ranking / Fusion)
- 캐시 전략과 세션 컨텍스트 설계

## 6. 자주 하는 실수
- 과도한 chunk_size, 부족한 overlap
- 비구조적 데이터에 맞지 않는 스플리터 선택
- 임베딩 모델/토큰화 불일치

## 7. 참고 링크
- (예시) 벡터DB 문서, LangChain 문서, LLM 제공사 베스트 프랙티스

```


---

## 분할된 청크 Output Data

### Chunk 1

```
page_content='작성자: 손용훈 | 버전: v1.2 | 업데이트: 2025-08-26' metadata={'Header 1': 'RAG 실전 가이드'}
```

---### Chunk 2

```
page_content='RAG(Retrieval-Augmented Generation)는 외부 지식 검색 결과를 LLM 입력에 주입하여, 최신성과 사실성을 강화하는 아키텍처입니다.' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '1. RAG란?'}
```

---### Chunk 3

```
page_content='- 최신성: 모델이 학습 이후의 정보를 모름
- 정확성: 환각(Hallucination) 감소
- 근거: 출처 링크 제공 가능' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '1. RAG란?', 'Header 3': '1.1 왜 필요한가'}
```

---### Chunk 4

```
page_content='1) 문서 수집 → 2) 텍스트 분할 → 3) 임베딩 → 4) 벡터DB 적재 → 5) 질의 시 검색 → 6) 프롬프트 구성 → 7) 생성' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '2. 파이프라인 구성'}
```

---### Chunk 5

```
page_content='- Character: 간단하지만 문맥 파손 위험
- RecursiveCharacter: 문맥 보존 우수(권장)
- MarkdownHeader: 문서 구조 반영
- Code/HTML/JSON 전용 스플리터: 포맷 맞춤형' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '2. 파이프라인 구성', 'Header 3': '2.1 텍스트 분할 전략'}
```

---### Chunk 6

```
page_content='**지시문 + 컨텍스트 + 제약조건 + 출력형식**의 4요소를 명시합니다.
예) "아래 컨텍스트만을 근거로 한국어로 5문장 요약하세요. 근거 문서 제목과 헤더를 함께 표기하세요."' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '3. 프롬프트 설계'}
```

---### Chunk 7

```
page_content='- 정답성(groundedness)
- 인용 정확도(citation precision)
- 재현성(reproducibility)
- 속도/비용' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '4. 품질 측정'}
```

---### Chunk 8

```
page_content='- 쿼리 리라이팅(Query Rewriting)
- 하이브리드 검색(BM25 + 벡터)
- 다단계 RAG(Routing / Re-Ranking / Fusion)
- 캐시 전략과 세션 컨텍스트 설계' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '5. 운영 팁'}
```

---### Chunk 9

```
page_content='- 과도한 chunk_size, 부족한 overlap
- 비구조적 데이터에 맞지 않는 스플리터 선택
- 임베딩 모델/토큰화 불일치' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '6. 자주 하는 실수'}
```

---### Chunk 10

```
page_content='- (예시) 벡터DB 문서, LangChain 문서, LLM 제공사 베스트 프랙티스' metadata={'Header 1': 'RAG 실전 가이드', 'Header 2': '7. 참고 링크'}
```

---