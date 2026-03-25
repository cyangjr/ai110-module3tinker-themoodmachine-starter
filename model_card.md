# Model Card: Mood Machine

This model card documents the Mood Machine project, which includes two mood classifiers:

1. Rule based model in mood_analyzer.py
2. Machine learning model in ml_experiments.py

## 1. Model Overview

**Model type:**  
I used and evaluated both the rule based and ML models.

**Intended purpose:**  
Classify short, informal text into one of four labels: positive, negative, neutral, or mixed.

**How it works (brief):**  
Rule based model: preprocess text, match known mood words, apply a scoring rule, then map score/signals to a label.  
ML model: convert text to bag-of-words features and train logistic regression on labeled examples.

## 2. Data

**Dataset description:**  
The dataset contains 14 posts total. I added 8 new posts to the starter set. New posts include slang, emojis, sarcasm, and mixed emotional tone.

**Labeling process:**  
Each post has one label in TRUE_LABELS. I used mixed when both positive and negative cues were present.

Hard-to-label examples:

- Feeling tired but kind of hopeful
- Coffee spilled on my notes but at least the quiz went good
- Missed the bus again... love that for me

These examples are ambiguous and could receive different labels from different annotators.

**Important characteristics of your dataset:**

- Contains slang and informal wording
- Contains emojis and punctuation-heavy style
- Includes sarcasm and irony
- Includes mixed-emotion and subtle-tone posts
- Includes short/ambiguous posts

**Possible issues with the dataset:**  
The dataset is very small, contains subjective labels for some posts, and has limited linguistic/cultural coverage.

## 3. How the Rule Based Model Works (if used)

**Your scoring rules:**

Preprocessing:

- Lowercase input text
- Regex tokenization for words/contractions

Scoring:

- Start at score 0
- Positive word: +1
- Negative word: -1
- Enhancement added: simple negation handling. If the previous token is not, never, no, or ends with n't, sentiment for the current mood word is flipped.

Label mapping:

- If both positive and negative signals appear, return mixed
- Else score > 0: positive
- Else score < 0: negative
- Else: neutral

**Strengths of this approach:**  
Transparent and easy to debug. Works reasonably for direct sentiment statements.

**Weaknesses of this approach:**  
Fails on sarcasm, subtle context, and out-of-vocabulary emotional words.

## 4. How the ML Model Works (if used)

**Features used:**  
Bag-of-words features with CountVectorizer.

**Training data:**  
Trained on SAMPLE_POSTS and TRUE_LABELS.

**Training behavior:**  
After dataset expansion, the ML model reached 1.00 accuracy on the same data it trained on, showing strong fit and high sensitivity to provided labels.

**Strengths and weaknesses:**

Strengths:

- Learns useful lexical patterns without hand-writing rules
- Correctly captured several mixed/slang examples in this dataset

Weaknesses:

- Easy to overfit with very small datasets
- Performance may drop on unseen language
- Learns label biases from human annotations

## 5. Evaluation

**How you evaluated the model:**  
Both models were evaluated on the labeled posts in dataset.py.

- Rule based accuracy: 0.50
- ML accuracy on training dataset: 1.00

**Examples of correct predictions:**

Rule based:

- I love this class so much -> positive
- Today was a terrible day -> negative
- This is fine -> neutral

ML:

- Feeling tired but kind of hopeful -> mixed
- Group chat is dead and I feel weirdly lonely tonight -> negative
- Coffee spilled on my notes but at least the quiz went good -> mixed

**Examples of incorrect predictions:**

Rule based errors:

- Feeling tired but kind of hopeful -> predicted negative, true mixed
  Reason: tired was recognized as negative, but hopeful was not in the positive lexicon.

- Missed the bus again... love that for me -> predicted positive, true negative
  Reason: literal match on love without sarcasm detection.

- Group chat is dead and I feel weirdly lonely tonight -> predicted neutral, true negative
  Reason: lonely/dead are not in the negative dictionary.

ML errors on this same split:

- None observed (0 errors), but this was training-set evaluation.

## 6. Limitations

- Dataset is small (14 posts)
- Labels are subjective for ambiguous/sarcastic text
- Rule based model misses unseen expressions and nuanced phrasing
- Sarcasm handling is weak
- ML metric is optimistic because evaluation was not on a held-out test set

## 7. Ethical Considerations

Potential risks:

- Misclassifying distress or negative mood as neutral/positive may cause harm in support contexts
- Slang, dialect, and culture-specific language may be misinterpreted
- Personal message analysis introduces privacy and consent concerns

Bias and scope note:

This model is optimized for short, English, informal posts similar to this dataset. It may underperform for other dialects, languages, and communication styles.

## 8. Ideas for Improvement

- Add more labeled data across broader language communities
- Expand lexicons for slang, emojis, and multi-word phrases
- Add phrase-level handling for expressions like no cap and sarcasm cues
- Evaluate ML with held-out test split or cross-validation
- Compare CountVectorizer with TF-IDF and n-gram features
- Report per-label precision/recall in addition to accuracy
