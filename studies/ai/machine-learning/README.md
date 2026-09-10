---
layout: page
title: Machine Learning
description: >
  머신러닝(EEE3314) 강의 노트와 프로그래밍 과제. 강의 내용과 과제 내용을 분리해 정리했습니다.
hide_description: false
sitemap: false
permalink: /studies/ai/machine-learning/
---

원본: [GitHub — Artificial Intelligence](https://github.com/RayKim3103/Undergraduate-Course/tree/main/%5BUndergraduate%5D_Artificial_Intelligence)

> **강의 노트**(`lecture_notes/`)와 **프로그래밍 과제**(`Regression/` · `Classifier/` · `NeuralNetwork/`)를 별도 파일로 분리했습니다. 강의 노트는 선형회귀·정규화·로지스틱회귀·신경망이 Part 1/2/3로 쪼개져 있던 것을 주제 단위로 합치고, 유도(normal equation·soft-threshold·bias–variance 분해·k-means 수렴·PCA/LDA·backprop 등)·표준 공식·수치 예제를 넣어 표준 ML 교재(ISL · ESL · CS231n) 수준으로 보강했습니다. 과제 노트는 과제 스펙과 **실제 측정 결과**(손실·정확도·precision/recall 등)를 원본 노트북 그대로 정리했습니다.

## 강의 노트

1. [머신러닝 개요](01-introduction.md)
2. [점추정과 Maximum Likelihood Estimation](02-point-estimation-mle.md)
3. [선형회귀 (모델·학습·성능평가·Bias–Variance)](03-linear-regression.md)
4. [정규화 회귀 (Ridge · Lasso · Cross-Validation)](04-regularized-regression.md)
5. [로지스틱 회귀 (분류 · 정규화 · SGD)](05-logistic-regression.md)
6. [결정 트리](06-decision-tree.md)
7. [앙상블 학습 (Bagging · Random Forest · Boosting)](07-ensemble-learning.md)
8. [분류기 평가 (Precision · Recall · PR curve)](08-evaluating-classifiers.md)
9. [Instance-Based Learning (k-NN · Kernel Regression)](09-instance-based-learning.md)
10. [군집화 (Clustering · K-Means)](10-clustering-k-means.md)
11. [차원 축소 (PCA · LDA)](11-dimension-reduction-pca-lda.md)
12. [신경망 (기초 · Backpropagation · 실전 학습)](12-neural-networks.md)
13. [CNN (합성곱 신경망)](13-convolutional-neural-networks.md)

## 과제 — 프로그래밍 프로젝트

1. [과제 1 — Regression (Linear · Ridge · Lasso · Elastic Net)](hw1-regression.md) — Communities and Crime 데이터, NumPy로 4종 회귀 구현 + overfitting/regularization 분석
2. [과제 2 — Ensemble (Random Forest · AdaBoost · Precision/Recall)](hw2-ensemble.md) — bagging vs boosting 직접 구현 + 지표·결정경계 분석
3. [과제 3 — Neural Networks (Flat · Modular · Dropout · Momentum)](hw3-neural-networks.md) — `make_moons`에 MLP 구현 (flat / PyTorch식 modular) + capacity·정규화·optimizer 분석
