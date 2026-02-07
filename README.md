# Aksit-Nath

## AI System Design: Detecting Fake Urgency & Psychological Manipulation

### 1) Objective & Scope
Build an AI system that detects psychological manipulation in text-only messages (SMS, messaging apps, email, job offers, payment alerts). The system must **explain** why a message is risky rather than simply blocking or labeling spam. It must be **strictly limited to language-based manipulation detection**, with **no URL scanning, malware detection, or cybersecurity checks**.

**Manipulation categories (multi-label):**
- **Urgency Pressure** (e.g., “act now,” “within 24 hours,” “last chance”)
- **Fear Triggers** (e.g., “account blocked,” “legal action,” “suspended”)
- **Authority Impersonation** (e.g., fake bank/government/HR)
- **Reward Bait** (e.g., “you won,” “free reward,” “selected”)
- **Guilt or Emotional Pressure** (e.g., “your family will suffer,” “don’t disappoint”)

### 2) Data Strategy
The dataset is designed to teach *manipulation detection* rather than spam detection. Each message is tagged with **one or more manipulation categories**, including **legitimate notifications** and **neutral text** to reduce false positives.

**Sources:**
1. **Public SMS/Email spam/phishing datasets**  
   - Re-label each item by manipulation type(s) instead of spam/ham.
2. **Synthetic, human-written messages**  
   - Realistic scam and legit notifications (shipping updates, receipts).
   - Neutral everyday messages (no manipulation).
   - Each message manually labeled with one or more manipulation categories.

**Label schema example:**
```json
{
  "text": "Your account will be suspended in 24 hours. Verify now!",
  "labels": ["Urgency Pressure", "Fear Triggers", "Authority Impersonation"],
  "source": "synthetic"
}
```

### 3) Feature Extraction (Language & Psychology)
To enable explainability and fast detection, extract interpretable features:
- **Urgency / time-based words** (e.g., “today,” “now,” “within 24 hours”)
- **Fear-related keywords** (e.g., “blocked,” “legal action,” “suspended”)
- **ALL CAPS usage**
- **Excessive punctuation** (e.g., “!!!”)
- **Conditional threats** (e.g., “if you don’t…”)
- **Authority cues** (e.g., “Bank,” “IRS,” “HR Department”)
- **Reward cues** (e.g., “winner,” “prize,” “selected”)

### 4) Hybrid Model Architecture
Combine transparent rules with contextual ML inference:

**A) Rule-Based Layer (fast + explainable)**
- Pattern matching for urgency, fear, authority cues, reward bait.
- Outputs:
  - Detected categories
  - Highlighted phrases
  - Rule confidence signal

**B) ML Layer (fine-tuned transformer for multi-label classification)**
- Model: **BERT or RoBERTa** fine-tuned on labeled dataset.
- Input: message text.
- Output: probabilities for each manipulation category.

**Ensemble logic:**  
Merge results from rule-based + ML layers:
- If rule-based matches strongly, include as detected category.
- Use ML to resolve nuanced cases or implicit pressure.

### 5) Explanation Engine
Each message produces a clear, human-readable explanation:

**Outputs:**
1. **Detected categories**
2. **Highlighted phrases** (from rules + model attention)
3. **Plain-language explanation**  
   Example:
   > “This message uses urgency pressure (“within 24 hours”) and fear triggers (“account suspended”) to push you into immediate action.”

### 6) Risk Scoring (0–100)
Score increases with **number and severity** of manipulative techniques:
- Base weights per category (e.g., Fear > Urgency).
- Bonuses for ALL CAPS, excessive punctuation, or explicit threats.
- Final score capped at 100 and shown to the user.

**Example scoring approach:**
```
Risk Score = Σ(category_weight * confidence) + style_bonus
```

### 7) User Interface (Minimal & Intuitive)
Any of the following interfaces are valid:
- Web application
- Browser extension
- Messaging bot

**UI should display:**
- Risk score
- Highlighted manipulative text
- Human-readable explanation

### 8) Evaluation
**Quantitative metrics:**
- Precision, recall, F1-score **per category**
- Confusion matrices

**Qualitative metrics:**
- Human usability evaluation:
  - Do users understand explanations?
  - Does it reduce risky actions?

### 9) Ethical Considerations
The system must:
- **Not store messages**
- **Not monitor users**
- **Not access links or personal data**
- **Allow full user control** over analysis

### 10) Future Enhancements
- **Multilingual support** (e.g., English + Hindi)
- **Analysis of voice call transcripts**
- **Elderly-focused protection modes**
- **Integration with popular messaging platforms**
