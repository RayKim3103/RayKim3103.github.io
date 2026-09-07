---
layout: page
title: "03. Classification Evaluation Metrics"
permalink: /studies/ai/computer-vision/03-3-classification-evaluation-metrics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Computer_Vision/lecture_notes/03-3%20Classification%20Evaluation%20Metrics.md)

{% raw %}
## 한눈에 보기
Classification 3는 분류 결과를 평가하는 지표를 다룬다. 특히 class imbalance가 있을 때 accuracy만 보는 것이 왜 위험한지 confusion matrix를 기준으로 설명한다.

## 핵심 개념
- confusion matrix
- accuracy
- precision
- recall
- specificity
- F1 score
- ROC curve
- precision-recall curve

## 체계적 정리
- confusion matrix는 TP, FP, FN, TN으로 prediction과 ground truth의 관계를 나눈다.
- accuracy는 전체 중 맞힌 비율이지만, negative class가 압도적으로 많으면 높은 accuracy가 무의미할 수 있다.
- precision은 positive라고 예측한 것 중 실제 positive 비율이고, recall은 실제 positive 중 찾아낸 비율이다.
- specificity는 실제 negative를 negative로 맞힌 비율이다.
- F1 score는 precision과 recall의 조화평균으로 두 지표의 균형을 본다.
- ROC와 PR curve는 threshold 변화에 따른 성능 trade-off를 시각화한다.

## 중요한 수식과 관점
- Accuracy: $(TP+TN)/(TP+TN+FP+FN)$.
- Precision: $TP/(TP+FP)$.
- Recall: $TP/(TP+FN)$.
- F1: $2PR/(P+R)$.

## 구현과 학습 포인트
Detection과 segmentation에서는 단순 accuracy보다 IoU, mAP, mIoU 같은 지표가 중요하다. 이 노트는 threshold와 class imbalance를 해석하는 기본 틀이다.

## 자주 헷갈리는 지점
- precision과 recall은 서로 trade-off를 갖는 경우가 많다.
- imbalanced data에서는 accuracy가 높아도 minority class recall이 낮을 수 있다.
- PR curve는 positive class가 드문 문제에서 ROC보다 더 직접적인 정보를 줄 수 있다.

## 복습 질문
- 이 자료가 다루는 입력, 출력, 목적함수 또는 기하학적 대상은 무엇인가?
- 같은 문제를 전통적 방법과 딥러닝 방법으로 풀 때 어떤 가정이 달라지는가?
- 실제 구현에서 shape, 좌표계, 확률 해석, train/eval mode 중 무엇을 가장 먼저 확인해야 하는가?

## 연결 노트
- [Classification 통합](03-classification-overview.md)
- [Detection and Segmentation](06-detection-and-segmentation.md)
- [기말고사 리뷰](99-final-exam-review.md)

{% endraw %}

---

이전: [03. Classification Regularization](03-2-classification-regularization.md) · 다음: [04. Neural Networks](04-neural-networks.md)
