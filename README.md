# 📚 Scientific Knowledge RAG
Advanced RAG를 활용한 과학 질의 응답 시스템.
elasticsearch와 Faiss를 활용하여 과학 질의에 가장 밀접한 데이터를 검색 후 해당 데이터를 토대로 GPT-4o가 응답을 생성하는 시스템.

![output](https://github.com/user-attachments/assets/a724e6a0-7568-4962-a324-c6374b9afcbe)

---

## 🔧 프로젝트 구조

```
.
├── data/                      # Input data: documents.jsonl, eval.jsonl
├── tools/                     # Core RAG components
│   ├── searchengine.py        # FAISS + Elasticsearch hybrid retrieval + reranker
│   ├── query_transformer.py   # Converts multi-turn chat to standalone queries
│   └── generate.py            # Generates answers from queries and references
├── main.py                    # Entry point: runs end-to-end RAG pipeline
├── .env                       # API keys and environment variables
├── .gitignore
├── EDA.ipynb                  # Data exploration
└── elasticsearch.ipynb        # Elasticsearch index setup & testing
```

---

## 🚀 실행 방법

### 1. 필수 환경 구성

```bash
sudo apt update
sudo apt install -y python3-pip
pip install -r requirements.txt
```

> `requirements.txt`는 직접 만들어야 해요. 사용된 주요 라이브러리는 아래 참조.

### 2. `.env` 파일 설정

```env
UPSTAGE_API_KEY=your_upstage_solar_api_key
```

### 3. Elasticsearch 실행 (로컬에서 실행할 경우)

```bash
sudo -u daemon -s bash -c "bash /upstage-ai-advanced-ir7/elasticsearch-8.8.0/bin/elasticsearch"
```

---

## 🧠 파이프라인 구성

1. **QueryTransformer**  
   → `eval.jsonl`의 멀티턴 메시지를 standalone 검색 쿼리로 변환  
2. **SearchEngine**  
   → FAISS + Elasticsearch로 문서 검색  
   → Ko-reranker 모델로 결과 rerank  
3. **Generate**  
   → 검색된 문서를 바탕으로 답변 생성 (`ko-gemma-2-9b-it`)  
4. **Output**  
   → `./outputs/sample_submission.csv`에 결과 저장

---

## 📦 주요 라이브러리

- `transformers`
- `faiss-cpu`
- `elasticsearch`
- `openai` (Upstage Solar용)
- `tqdm`
- `dotenv`
- `torch`, `numpy`

---

## 📁 데이터 형식 예시

### `documents.jsonl`

```json
{"docid": "1", "content": "물은 100도씨에서 끓는다."}
```

### `eval.jsonl`

```json
{
  "eval_id": "001",
  "msg": [
    {"role": "user", "content": "물이 끓는 온도는 몇도야?"}
  ]
}
```

---

## ✅ 결과 예시

```json
{
  "eval_id": "001",
  "standalone_query": "물이 끓는 온도",
  "topk": ["1", "4", "9"],
  "references": [...],
  "answer": "물은 섭씨 100도에서 끓습니다. (참조: 문헌 0)"
}
```

---

## 📌 TODO (선택)

- [ ] Docker 환경 구성
- [ ] Streamlit 기반 웹 데모 추가
- [ ] Retrieval 성능 평가 (e.g. Top-k recall)
- [ ] Mistral 등 다른 LLM 교체 실험
