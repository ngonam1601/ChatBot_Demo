
Ứng dụng chatbot thông minh sử dụng AI để trả lời các câu hỏi về quy định giao thông dựa trên Luật Giao Thông Đường Bộ Việt Nam (NĐ 168/2024/NĐ-CP).

##  Tính Năng

- **Hỏi đáp tức thời**: Trả lời các câu hỏi về giao thông một cách nhanh chóng
- **Trích dẫn chính xác**: Cung cấp Điều, Khoản, Điểm liên quan từ luật
- **Ghi nhớ lịch sử**: Hỗ trợ hội thoại nhiều lượt (multi-turn conversation)
- **Giao diện đẹp**: Thiết kế hiện đại, dễ sử dụng
- **Hỗ trợ tiếng Việt**: Hiểu rõ câu hỏi bằng tiếng Việt

##  Kiến Trúc Hệ Thống

### Stack Công Nghệ

- **LLM**: Google Gemini 2.5 Flash
- **Embeddings**: Sentence Transformers (multilingual-e5-large)
- **Vector Store**: DocArrayInMemorySearch
- **Retrieval**: Hybrid (Vector Search + BM25)
- **Web Framework**: Gradio
- **Language Chain**: LangChain

### Quy Trình Xử Lý

```
User Question
    ↓
Retriever (Hybrid: Vector + BM25)
    ↓
Document Ranking & Deduplication
    ↓
Context Formatting
    ↓
LLM Prompt Generation
    ↓
Gemini AI Response
    ↓
Chat History Management
    ↓
User Response
```

##  Yêu Cầu Hệ Thống

- Python 3.8+
- pip hoặc conda

##  Cài Đặt & Chạy

### 1. Clone/Tải Dự Án

```bash
cd c:\Users\ngona\Downloads\DeepL
```

### 2. Cài Đặt Thư Viện

```bash
pip install -r requirements.txt
```

**Các thư viện chính:**
- `langchain`
- `langchain-google-genai`
- `sentence-transformers`
- `gradio`
- `pdfplumber`
- `numpy`
- `pandas`

### 3. Cấu Hình API Key

Mở file `project - Copy (2).ipynb` và thay thế API Key Gemini:

```python
os.environ["GOOGLE_API_KEY"] = "YOUR_GOOGLE_API_KEY_HERE"
```

**Lấy API Key từ:** https://aistudio.google.com/app/apikey

### 4. Chuẩn Bị Dữ Liệu

Đảm bảo có các file:
- `luatgtdb.pdf` - Tài liệu PDF của Luật Giao Thông
- `luatgtdb_chunks.json` - Chunks đã xử lý (auto-generated nếu không có)
- `evaluation.json` - Dataset đánh giá (tùy chọn)

### 5. Chạy Ứng Dụng

Chạy Jupyter Notebook:

```bash
jupyter notebook "project - Copy (2).ipynb"
```

Hoặc chạy từng cell theo thứ tự để khởi tạo:
1. Import thư viện
2. Xử lý PDF → Tạo chunks
3. Khởi tạo LLM & Embeddings
4. Tạo Vector Store
5. Khởi tạo Retrievers
6. Xây dựng RAG Chain
7. Chạy Gradio Interface

### 6. Truy Cập Ứng Dụng

Sau khi chạy cell cuối, ứng dụng sẽ mở tại:
```
http://127.0.0.1:7860
```

##  Cấu Hình

### Thay Đổi Retriever

Mở cell "XÂY DỰNG RAG CHAIN" và thay đổi:

```python
# Mặc định: Vector Search
retriever = vector_retriever

# Hoặc BM25
retriever = bm25_retriever

# Hoặc Hybrid (khuyến nghị)
retriever = hybrid_retriever
```

### Tuning LLM

Trong cell khởi tạo LLM:

```python
llm = ChatGoogleGenerativeAI(
    model="gemini-2.5-flash",
    temperature=0.2,  # 0-1: 0=deterministic, 1=creative
    max_output_tokens=2048
)
```

### Tuning Embedding

```python
embeddings = STEmbedder("intfloat/multilingual-e5-large")
# Có thể đổi model nếu cần
```

## Metrics & Đánh Giá

Chạy cell "TÍNH THÊM METRIC: PRECISION, NDCG, HIT RATE" để xem:
- **Hit Rate**: % câu hỏi có kết quả
- **MRR**: Mean Reciprocal Rank
- **Precision@5/10**: Chính xác ở top 5/10
- **Recall@5/10**: Khôi phục ở top 5/10
- **NDCG@5/10**: Normalized DCG score

##  Tùy Chỉnh Giao Diện

CSS custom nằm trong cell cuối:

```python
custom_css = """
:root { --radius: 14px; }
body { background: linear-gradient(...); }
.gradio-container { ... }
#chatbot { height: 700px !important; }
"""
```

Thay đổi:
- `height`: Chiều cao chatbot (px)
- `background`: Màu nền
- `max-width`: Chiều rộng container
- Avatar URLs: Hình đại diện

## 🔧 Xử Lý Sự Cố

### Lỗi API Key
```
Error: 403 Forbidden
```
**Giải pháp**: Kiểm tra API Key ở https://aistudio.google.com/app/apikey

### Lỗi File Không Tìm Thấy
```
FileNotFoundError: luatgtdb.pdf
```
**Giải pháp**: Đặt file PDF đúng đường dẫn hoặc chỉnh `PDF_PATH` trong code

### Lỗi Memory
```
MemoryError: Unable to allocate ...
```
**Giải pháp**: Giảm số chunks hoặc số kết quả retrieval (k=30 → k=10)

### Embedding quá chậm
**Giải pháp**: 
- Dùng model nhẹ hơn: `sentence-transformers/all-MiniLM-L6-v2`
- Giảm số documents
- Dùng GPU (CUDA) nếu có

## 📝 Ví Dụ Sử Dụng

### Câu Hỏi 1: Vượt Đèn Đỏ
**Input**: "Mức phạt khi vượt đèn đỏ là bao nhiêu?"

**Output**:
```
Theo Luật 168/2024/NĐ-CP [Điều X] [Khoản Y]:
- Mức phạt tiền: 6-8 triệu đồng
- Tước bằng: 3 tháng
```

### Câu Hỏi 2: Nồng Độ Cồn
**Input**: "Giới hạn nồng độ cồn là bao nhiêu?"

**Output**:
```
[Điều X] [Khoản Y]:
- Người lái xe: ≤ 20mg/100ml máu
- Không lái xe nếu: > 50mg/100ml
```

##  Đóng Góp

Để cải thiện:
1. Thêm documents vào `luatgtdb.pdf`
2. Cập nhật `train.json` với câu hỏi mới
3. Chạy lại chunk processing
4. Đánh giá metrics

##  Tài Liệu Tham Khảo

- [LangChain Docs](https://python.langchain.com/)
- [Gradio Docs](https://gradio.app/)
- [Google Gemini API](https://aistudio.google.com/)
- [Sentence Transformers](https://www.sbert.net/)

##  Lưu Ý Pháp Lý

- Chatbot này là công cụ hỗ trợ, không phải tư vấn pháp luật chính thức
- Luôn kiểm tra thông tin trên các nguồn chính thức
- Pháp luật có thể thay đổi, cập nhật thường xuyên

## 📄 License

Dự án này sử dụng dữ liệu công khai của Luật Giao Thông Đường Bộ Việt Nam.

---
