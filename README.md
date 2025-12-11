# 업태·종목 기반 자금종류 추천 시스템

![image](https://github.com/user-attachments/assets/f59dc6ae-4189-4fd7-8351-6d5c64f6cf92)

이 저장소는 여러 임베딩 모델과 Qdrant를 사용하여 **업태와 종목을 입력하면 적합한 자금종류를 추천**하는 시스템을 구현한 Jupyter 노트북을 포함합니다.

## 📚 노트북 버전

### 1. **sample.ipynb** - BGE-M3 버전 (기본, 추천) ⭐
- **모델**: BGE-M3 (568M 파라미터)
- **검색 방식**: Sparse + Dense 하이브리드
- **특징**: BGE-M3 내장 Sparse 벡터 사용
- **한국어 성능**: 우수
- **추천 대상**: 처음 사용자, 일반적인 사용

### 2. **sample_qwen3_bm25.ipynb** - Qwen3 + BM25 버전 (비교 테스트용) 🚀
- **모델**: Qwen3-Embedding-0.6B (600M 파라미터) + BM25
- **검색 방식**: BM25 (Sparse) + Qwen3 Dense 하이브리드
- **특징**: 최신 Qwen3 모델과 전통적인 BM25 결합
- **한국어 성능**: 테스트 필요
- **추천 대상**: 다른 모델과 성능 비교를 원하는 사용자

## 핵심 기능

### BGE-M3 버전 (sample.ipynb)
BGE-M3 올인원 임베딩 모델 사용:
- **Dense vectors**: 의미론적 유사도 측정 (1024 차원)
- **Sparse vectors**: BGE-M3 내장 희소 벡터로 키워드 매칭
- 단일 모델에서 다중 벡터 생성으로 효율적

### Qwen3 + BM25 버전 (sample_qwen3_bm25.ipynb)
최신 모델과 전통 방식의 결합:
- **Dense vectors**: Qwen3-Embedding으로 의미론적 유사도 측정
- **Sparse vectors**: BM25 알고리즘으로 키워드 매칭
- 서로 다른 강점을 가진 두 방식의 하이브리드

## 요구사항

- Python 3.9+
- Jupyter Notebook 또는 Google Colab
- (Docker 불필요 - 인메모리 모드 사용)

## 동작 방식

### 공통 프로세스
1. **데이터 로딩**: 업태, 종목, 자금종류 데이터를 CSV 파일에서 로드 (280개 레코드)
2. **텍스트 포맷팅**: 업태와 종목 정보를 임베딩용 텍스트로 포맷
3. **임베딩 생성**: 각 모델로 Dense 벡터 생성
4. **인덱싱**:
   - Dense 벡터 → Qdrant에 저장
   - Sparse: BGE-M3 내장 또는 BM25 별도 인덱스
5. **하이브리드 검색**: Sparse + Dense 결과 결합 및 중복 제거
6. **점수 임계치 필터링**: threshold 이상의 결과만 반환
7. **결과 표시**: 추천 자금종류 키워드 출력

## 사용 방법

### Google Colab에서 실행
1. 원하는 노트북 파일을 Colab에 업로드
   - **처음 사용**: `sample.ipynb` 추천
   - **비교 테스트**: `sample_qwen3_bm25.ipynb`
2. `funding_data.csv` 파일도 함께 업로드
3. 메뉴: `런타임` → `모두 실행`
4. Step 14에서 업태/종목 입력하여 테스트
   - 종료하려면 `q` 또는 `종료` 입력

## 주요 파라미터

- **limit**: 반환할 최대 결과 개수 (기본값: 10)
- **threshold**: 최소 점수 임계치 (기본값: 0.8)
  - 0.8~1.0: 고품질 결과만
  - 0.5~0.7: 더 많은 후보 포함
  - 0.0~0.4: 탐색적 검색
