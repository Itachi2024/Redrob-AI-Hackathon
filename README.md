# Redrob AI Hackathon: Candidate Discovery and Ranking

**AI-Powered Recruitment Pipeline** for finding and ranking Senior AI Engineers from a dataset of 100,000 candidates.

## 📋 Project Overview

This project implements an **end-to-end candidate discovery and ranking system** that processes large-scale candidate data, applies intelligent filtering, and uses semantic matching to identify the best fit for a Senior AI Engineer role at Redrob.

### Key Results
- **Total Candidates**: 100,000
- **Honeypots Detected**: 5,991 (5.99%)
- **Clean Candidates**: 94,009
- **Runtime**: ~3 minutes
- **Output**: `submission.csv` with top 100 ranked candidates

---

## 🎯 Methodology

### Stage 1: Data Loading & EDA
- Load 487 MB JSONL dataset (~11s)
- Extract candidate profiles, career history, skills, and engagement signals
- Generate 6-panel exploratory analysis

### Stage 2: Skills Landscape Analysis
- 133 unique skills across all candidates
- Proficiency distribution: Intermediate (44%), Beginner (35%), Advanced (10%), Expert (0.1%)
- AI/ML keyword extraction and clustering

### Stage 3: Honeypot Detection (7 Heuristics)
Automatically flags fake/low-quality profiles:

| Flag | Description | Count |
|------|-------------|-------|
| `title_skill_mismatch` | Non-tech title with 6+ AI skills | 5,517 |
| `no_tech_in_career` | Non-tech title, no ML mention in career | 5,373 |
| `identical_descriptions` | 3+ identical career descriptions | 447 |
| `impossible_exp` | Stated YoE >> actual career duration | 24 |
| `expert_zero_duration` | 3+ expert skills with <6 months each | 21 |

### Stage 4: TF-IDF Semantic Matching
- Build candidate profile text (headline + summary + career + skills + education)
- Fit TF-IDF vectorizer on all 94,010 documents (10K vocab, bigrams)
- Compute cosine similarity against job description
- Normalize scores: [0.0021, 0.5037] → [0, 1] range
- Mean similarity: 0.0300

### Stage 5: Multi-Feature Scoring (11 Dimensions)
Weighted score aggregation:

| Feature | Weight | Details |
|---------|--------|---------|
| Semantic match | 25% | TF-IDF cosine similarity |
| Core skills | 20% | Hard skills match (embeddings, vector DB, PyTorch, etc.) |
| Years of experience | 15% | Gaussian around 7yr (ideal ±2yr) |
| Title relevance | 12% | "Engineer", "ML", "AI", "Data" keywords |
| Education level | 8% | Bachelor's/Master's degree boost |
| Location match | 8% | Pune/Noida preferred, NCR secondary |
| Engagement signals | 7% | GitHub activity, open-to-work flag |
| Nice-to-have skills | 3% | LLM fine-tuning, learning-to-rank, etc. |
| Production keywords | 1% | "shipped", "deployed", "production" bonus |
| Notice period | 1% | <30 days preferred |
| Anti-signals | × 0.5-0.8 penalty | Low response rate, long notice, etc. |

### Final Ranking
- Sort by composite score (highest first)
- Output top 100 candidates to `submission.csv`
- Include metadata: score components, reasoning, data quality indicators

---

## 📊 Dataset Insights

### Candidate Profile Statistics
- **Average Experience**: 7.2 years
- **Average Skills per Candidate**: 9.6
- **Open to Work**: 35.3%
- **Unique Job Titles**: 47
- **Countries Represented**: 8
- **Top Locations**: India (vast majority), with Pune/Noida, Delhi NCR, Bangalore

### Skill Distribution
- **Top AI/ML Skills**: Machine Learning, Deep Learning, Python, PyTorch, TensorFlow
- **Vector DB Adoption**: Pinecone, Weaviate, Qdrant, Milvus, FAISS
- **NLP Specialization**: BERT, Transformers, Sentence-BERT, GPT
- **Production Tools**: MLOps, Kubernetes, Docker, Spark

---

## 🔧 Technical Stack

### Environment
- **Platform**: Google Colab (Free Tier)
- **Python**: 3.12.13
- **CPU**: 2 cores
- **RAM**: 12 GB
- **GPU**: Not required
- **OS**: Linux (Google Colab)

### Libraries
```
Data Processing: pandas, numpy
Text & NLP: scikit-learn (TF-IDF), regex
ML: sklearn.metrics.pairwise (cosine_similarity)
Visualization: matplotlib, seaborn, gridspec
Utilities: tqdm, json, csv, datetime
```

### Key Dependencies
```
pandas>=1.3.0
numpy>=1.21.0
scikit-learn>=0.24.2
matplotlib>=3.4.0
seaborn>=0.11.0
tqdm>=4.62.0
```

---

## 🚀 Usage

### Option 1: Google Colab (Recommended)
1. Open notebook: [AI_Recruiter_Colab_Complete.ipynb](./AI_Recruiter_Colab_Complete.ipynb)
2. Click "Open in Colab" or upload to your Google Drive
3. Run all cells sequentially (order matters)
4. Download `submission.csv` from Colab output

### Option 2: Local Environment
```bash
# Clone repo
git clone https://github.com/Itachi2024/Redrob-AI-Hackathon.git
cd Redrob-AI-Hackathon

# Install dependencies
pip install pandas numpy scikit-learn matplotlib seaborn tqdm

# Run notebook (requires Jupyter)
jupyter notebook AI_Recruiter_Colab_Complete.ipynb

# Or convert to Python script
jupyter nbconvert --to script AI_Recruiter_Colab_Complete.ipynb
python AI_Recruiter_Colab_Complete.py
```

### Reproduce Command
```
Run all cells in AI_Recruiter_Colab_Complete.ipynb
Expected output: submission.csv (100-500 KB, <5min runtime)
```

---

## 📁 Input & Output

### Input
- **File**: `candidates.jsonl`
- **Size**: ~487 MB
- **Format**: JSON Lines (one candidate per line)
- **Location**: Auto-detected from:
  - `/content/drive/MyDrive/candidates.jsonl`
  - Or upload via Colab file picker

### Output
- **submission.csv**: Top 100 ranked candidates
  - Columns: `candidate_id`, `score`, `rank`, `component_scores` (skill, exp, etc.)
  - Sorted: Highest score first
  - Size: ~50-100 KB

---

## 🎓 Job Description Specifications

### Role
**Senior AI Engineer** at Redrob AI

### Must-Have Skills
- Production embedding & retrieval systems
- Vector databases: Pinecone, Weaviate, Qdrant, Milvus, FAISS
- Python (production-grade)
- Ranking metrics: NDCG, MRR, MAP, Precision@K
- Search/ranking/recommendation systems (shipped)
- Applied NLP & ML in production
- Deep learning: PyTorch or TensorFlow
- Sentence transformers & semantic search

### Nice-to-Have
- LLM fine-tuning (LoRA, QLoRA, PEFT)
- Learning-to-rank algorithms
- HR-tech domain knowledge
- Distributed systems
- Open-source contributions
- XGBoost, LightGBM for ranking

### Location & Experience
- **Location**: Pune or Noida, India (Hybrid)
- **Experience**: 5-9 years (flexible for exceptional candidates)
- **Ideal Profile**: 6-8 years total, 4-5 in ML/AI at product companies

---

## 📈 Performance Metrics

### Processing Pipeline
| Stage | Task | Time | Candidates |
|-------|------|------|-----------|
| 1 | Data loading | 11.3s | 100,000 |
| 2 | Skills analysis | 2-3s | 100,000 |
| 3 | Honeypot detection | 7.5s | 100,000 |
| 4 | TF-IDF + similarity | 27.0s | 94,009 |
| 5 | Scoring & ranking | 5-10s | 94,009 |
| **Total** | **Full pipeline** | **~182s (3 min)** | **94,009 final** |

### Quality Metrics
- **Honeypot Detection Rate**: 5.99% (5,991 / 100,000)
- **False Positive Estimate**: <2% (validated manually on samples)
- **Semantic Score Range**: 0.0021 to 0.5037 (wide spread indicates diversity)
- **Mean Similarity**: 0.0300 (sparse similarity typical for large-scale matching)

---

## 🔍 Example: Honeypot Detection

### Sample Flagged Profile
```
ID: CAND_0000021
Title: Project Manager (Non-tech)
AI Skills Count: 8 (Machine Learning, Deep Learning, PyTorch, etc.)
Career History: All PM/Operations roles
Flags:
  ✗ title_skill_mismatch (8 AI skills in PM title)
  ✗ no_tech_in_career (career desc has no ML/AI mentions)
Status: HONEYPOT → Excluded from ranking
```

### Passing Profile (Clean)
```
ID: CAND_0050234
Title: Senior ML Engineer
YoE: 6 years
Core Skills: 12 (PyTorch, Pinecone, NLP, BERT, etc.)
Career History: 2 ML/AI roles (descriptions match expertise)
Education: B.Tech Computer Science
Flags: None ✓
Status: CLEAN → Included in ranking
```

---

## 🛠️ Customization

### Modify Job Description
Edit the `JD_TEXT` variable in Stage 4:
```python
JD_TEXT = """
Your custom job description here...
"""
```

### Adjust Weights
Modify scoring weights in Stage 5:
```python
CORE_SKILLS = [...]      # Add/remove required skills
NICE_SKILLS = [...]      # Add nice-to-have skills
IDEAL_YOE_CENTER = 7.0   # Target years of experience
PREFERRED_LOCATIONS = {} # Update location preferences
```

### Change Honeypot Thresholds
Adjust heuristic sensitivity in Stage 3:
```python
if ai_count >= 6:  # Change threshold from 6 to N
    flags.append("title_skill_mismatch")
```

---

## 📋 Team & Attribution

### Team: GIIGALOGY
| Name | Email | Role |
|------|-------|------|
| **Sourabh Joshi** | sourabhjoshi2310@gmail.com | AI Engineer (Lead) |
| **Aditya Dadhich** | adityadadhichofficial@gmail.com | AI Engineer |
| **KB Dhanush** | kbdhanush1528@gmail.com | AI Engineer |

### Contact
📧 **Primary Contact**: SOURABH JOSHI (sourabhjoshi2310@gmail.com)  
📱 **Phone**: +91-7073184255

---

## ✅ Declarations & Quality Assurance

- ✅ **Original Work**: 100% original code and methodology
- ✅ **No Collusion**: Independently developed
- ✅ **Honeypot Validation**: All 7 detection heuristics validated on samples
- ✅ **Reproducibility**: Tested end-to-end in Colab free tier
- ✅ **Specification Compliance**: Follows all hackathon submission requirements

### Tested Scenarios
- ✓ Fresh Colab session with auto-detection of `candidates.jsonl`
- ✓ Direct file upload fallback when auto-detect fails
- ✓ All 5 EDA visualizations render correctly
- ✓ Honeypot detection flags verified on sample profiles
- ✓ Submission CSV generated with correct format
- ✓ Runtime <5 min on Colab free tier

---

## 📚 References & Resources

### Semantic Matching
- TF-IDF Vectorization: [scikit-learn docs](https://scikit-learn.org/stable/modules/feature_extraction.html#tfidf-term-weighting)
- Cosine Similarity: [sklearn.metrics.pairwise](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.pairwise.cosine_similarity.html)

### Ranking Metrics
- NDCG (Normalized Discounted Cumulative Gain): [Wikipedia](https://en.wikipedia.org/wiki/Discounted_cumulative_gain)
- MRR (Mean Reciprocal Rank): [Info Retrieval theory](https://en.wikipedia.org/wiki/Mean_reciprocal_rank)

### Vector Databases
- [Pinecone](https://www.pinecone.io/)
- [Weaviate](https://weaviate.io/)
- [Qdrant](https://qdrant.tech/)
- [Milvus](https://milvus.io/)

### AI Tools Used
- **Claude**: Architecture design and code review
- **Gemini**: Logic validation and documentation

---

## 📄 License

This project is submitted as part of the **Redrob AI Hackathon**. All rights reserved by team GIIGALOGY.

---

## 🤝 Contributing

For questions, suggestions, or improvements:
1. Contact: sourabhjoshi2310@gmail.com
2. Open an issue in this repository
3. Submit a pull request with improvements

---

## 🎉 Acknowledgments

Special thanks to:
- **Redrob AI** for the hackathon opportunity
- **Google Colab** for free compute resources
- **Open-source community** for scikit-learn, pandas, and visualization libraries

---

**Last Updated**: June 26, 2026  
**Status**: Submission Ready ✅
