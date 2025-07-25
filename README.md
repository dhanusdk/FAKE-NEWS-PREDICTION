

import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import PassiveAggressiveClassifier
from sklearn.metrics import accuracy_score, confusion_matrix


df = pd.read_csv("/content/news.csv")


print("Sample data:\n", df.head())


df = df.dropna()


X = df['text']
y = df['label']


X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=7)

tfidf_vectorizer = TfidfVectorizer(stop_words='english', max_df=0.7)


tfidf_train = tfidf_vectorizer.fit_transform(X_train)
tfidf_test = tfidf_vectorizer.transform(X_test)


model = PassiveAggressiveClassifier(max_iter=50)
model.fit(tfidf_train, y_train)


y_pred = model.predict(tfidf_test)


accuracy = accuracy_score(y_test, y_pred)
print(f"\nModel Accuracy: {round(accuracy * 100, 2)}%")

conf_matrix = confusion_matrix(y_test, y_pred)
print("\nConfusion Matrix:\n", conf_matrix)


def predict_news(text):
    vectorized = tfidf_vectorizer.transform([text])
    prediction = model.predict(vectorized)
    return prediction[0]


sample = "NASA confirms discovery of a new Earth-like planet in nearby galaxy."
result = predict_news(sample)
print(f"\nPrediction for sample news:\n'{sample}' => {result}")
