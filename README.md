# Emotions Detection Model

Emotion detection is the study of using artificial intelligence to identify and categorize emotions expressed in text. By applying machine learning algorithms to analyze written communication, researchers aim to uncover insights into human sentiment and behavior. This field holds great potential for applications in various domains, including mental health, customer service, and social media analysis. However, it also presents challenges related to accuracy, ethics, and cultural context. Overall, emotion detection represents an exciting frontier in understanding and leveraging human emotions in the digital era.

![3585223_66180](https://github.com/tuanng1102/sentiment-analysis-app/assets/147653892/d0dc28c7-4244-43e4-bba4-9ea52e85a5af)

## Project Overview

Emotion detection, a quintessential task in natural language processing (NLP), involves discerning and categorizing human emotions expressed in textual data. Our project delves into this captivating domain, employing advanced machine learning techniques to classify emotions accurately. By leveraging the rich tapestry of human language, our model strives to decode the subtle nuances of human emotion, offering valuable insights into the complexities of human expression.

## Key Dependancies

- Our project relies on several essential libraries and tools:
  - ```pandas```: This library is indispensable for data manipulation and analysis, providing powerful data structures and tools for working with structured data.
  - ```numpy```: NumPy is the fundamental package for scientific computing in Python, providing support for large, multi-dimensional arrays and matrices, along with a collection of mathematical functions to operate on these arrays.
  - ```matplotlib.pyplot```: Matplotlib is a comprehensive library for creating static, animated, and interactive visualizations in Python. We specifically import the pyplot module for creating plots and charts.
  - ```seaborn```: Seaborn is a statistical data visualization library built on top of Matplotlib. It provides a high-level interface for drawing attractive and informative statistical graphics.
  - ```re```: The re module provides support for working with regular expressions in Python, which are powerful tools for pattern matching and text manipulation.

## Data Preprocessing
### 1 - Import libraries

We begin our journey by importing the necessary libraries and tools, including Pandas, NumPy, Matplotlib, Seaborn, and NLTK. With these at our disposal, we're well-equipped to tackle the challenges of data preprocessing.


``` bash
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings("ignore")
```

### 2 - Import dataset

Our dataset comprises textual data samples annotated with corresponding emotion labels. Leveraging Pandas, we import the dataset, extracting pertinent columns to facilitate subsequent processing steps.


``` bash
df = pd.read_csv("data/text.csv")
data = df[["text","label"]]
```

### 3 - Visualize dataset

Visual exploration of the dataset's emotion label distribution via bar plots and pie charts affords valuable insights into the prevalence and distribution of different emotions, guiding our preprocessing strategies.


``` bash
# Bar plot
data.label.value_counts().plot(kind="bar", color="red")
```

![value_count](https://github.com/tuanng1102/sentiment-analysis-app/assets/147653892/375f8e0e-7230-4edf-acbc-37b4d3338b66)

``` bash
# Pie plot
plt.figure(figsize=(10,6))
data.label.value_counts().plot(kind="pie", autopct="%.1f%%")
```

![value_count_pie](https://github.com/tuanng1102/sentiment-analysis-app/assets/147653892/d9366aa7-b105-4de2-8187-81de43351dca)

``` bash
# Set emotion labels
emotion_labels = {
0 : "sadness",
1 : "joy",
2 : "love",
3 : "anger",
4 : "fear",
5 : "surprise"
}
```


### 4 - Clean text

Textual data often harbors noise and irrelevant information that can impede model performance. Leveraging NLTK's text preprocessing capabilities, we meticulously clean the textual data, employing techniques such as stopword removal and stemming to enhance its quality and relevance.


``` bash
from nltk.stem.porter import PorterStemmer
from nltk.corpus import stopwords
stopwords_set = set(stopwords.words('english'))

def Clean_text(text):
    porter = PorterStemmer()
    text = [porter.stem(word) for word in text.split() if word not in stopwords_set]
    return " ".join(text)

data["text"] = data["text"].apply(lambda x: Clean_text(x))
```

### 5 - Splitting dataset to X and y

The dataset is partitioned into feature (X) and target (y) sets, laying the groundwork for model training and evaluation. This step ensures that our model learns from a subset of the data while retaining unseen examples for robust evaluation.


``` bash
X = data.text.values
y = data.label.values
```

### 6 - Vectorizing with TF-IDF

To transform the textual data into numerical features amenable to machine learning algorithms, we employ the TF-IDF vectorization technique. This process converts each text document into a numerical representation, capturing the importance of words in individual documents and across the dataset.


``` bash
from sklearn.feature_extraction.text import TfidfVectorizer
tfidf = TfidfVectorizer()
X = tfidf.fit_transform(X)
```

### 7 - Splitting dataset to Training-set and Test-set

The dataset is split into training and test sets to facilitate model training and evaluation, ensuring that our model's performance is rigorously assessed on unseen data, thereby gauging its ability to generalize to real-world scenarios.


``` bash
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

## MODELLING
### 1 - Training Logistic Regression model

Utilizing the training data and the TF-IDF transformed features, we train a logistic regression model—a popular choice for text classification tasks. This model learns to predict the probability distribution of emotion categories based on the input textual data.


``` bash
from sklearn.linear_model import LogisticRegression
log = LogisticRegression()
log.fit(X_train, y_train)
```

### 2 - Predict model

Once trained, our logistic regression model is deployed to generate predictions on the test data, enabling us to evaluate its performance and efficacy in accurately classifying emotions in unseen textual samples.


``` bash
y_pred_log = log.predict(X_test)
```

### 3 - Model Evaluation

Comprehensive model evaluation entails analyzing performance metrics such as precision, recall, and F1-score via the classification report. Additionally, visualization of the confusion matrix provides valuable insights into the model's predictive behavior across different emotion categories.


``` bash
from sklearn.metrics import confusion_matrix, classification_report, ConfusionMatrixDisplay
print(classification_report(y_test, y_pred_log))
```

![metric](https://github.com/tuanng1102/sentiment-analysis-app/assets/147653892/8a53defd-b3a3-4356-8e47-c3f5de826b5c)

``` bash
cm_log = confusion_matrix(y_test, y_pred_log, labels=log.classes_)
ConfusionMatrixDisplay(cm_log).plot()
```

![confusion-matrix](https://github.com/tuanng1102/sentiment-analysis-app/assets/147653892/350606a4-4e9a-4ef9-a10f-1173e08f73fb)

### 4 - Save model

To ensure the model's longevity and reusability, we serialize both the trained logistic regression model and the TF-IDF vectorizer using the pickle library, facilitating seamless deployment and integration into downstream applications.


``` bash
import pickle
pickle.dump(log,open('model/log.pkl','wb'))
pickle.dump(tfidf,open('model/tfidf.pkl','wb'))
```

## Conclusion

In essence, our Emotion Detection Model represents a convergence of cutting-edge machine learning techniques and linguistic insights, enabling us to unravel the intricacies of human emotion expressed through textual data. By harnessing the power of data-driven methodologies, we strive to enhance our understanding of human expression and pave the way for innovative applications in sentiment analysis, mental health monitoring, and beyond. Join us on this academic odyssey as we embark on a journey to decipher the language of the heart and unravel the mysteries of human emotion.
