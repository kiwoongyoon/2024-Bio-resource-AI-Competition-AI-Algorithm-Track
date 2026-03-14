# 🧬 Cancer-Type-Classification-from-Genomic-Variants

암환자의 **유전체 변이 데이터**를 기반으로 **26개의 암종(Subclass)을 분류하는 AI 모델**을 개발한 프로젝트입니다.

본 프로젝트는 **2024 생명연구자원 AI활용 경진대회 (DACON)** 에 참가하며 진행되었습니다.

---

# 📊 대회 개요

- **대회명:** 2024 생명연구자원 AI활용 경진대회  
- **주최 / 주관:** 과학기술정보통신부, 한국생명공학연구원, 국가생명연구자원정보센터  
- **운영:** DACON  
- **참가 대상:** 대한민국 국민 (개인 또는 팀 최대 3명)  

## 🎯 대회 목표

암환자의 **유전체 변이 정보(Genomic Variant Data)** 를 활용하여  
각 샘플이 **어떤 암종(Subclass)에 해당하는지 분류하는 AI 모델을 개발**하는 것이 목표입니다.

복잡한 바이오 데이터를 분석하여 **AI 기술이 실제 생명과학 문제 해결에 어떻게 활용될 수 있는지 탐구**하는 것을 목적으로 합니다.

---

# 🏆 최종 결과

- **Final Rank:** **39 / 1827**
- **Evaluation Metric:** Macro F1 Score

### 리더보드 평가 방식

| 평가 | 설명 |
|-----|-----|
| Public Score | 테스트 데이터 30% |
| Private Score | 테스트 데이터 100% |

---

# 📂 목차

1. 대회 배경  
2. 문제 정의  
3. 데이터 설명  
4. 접근 전략  
5. 데이터 전처리  
6. Feature Engineering  
7. 모델링  
8. 실험 결과  
9. 회고  

---

# 1️⃣ 대회 배경

최근 생명과학 분야에서는 유전체 데이터의 규모가 급격히 증가하고 있으며, 이를 효율적으로 분석하기 위해 **인공지능 기반 분석 기술**의 중요성이 커지고 있습니다.

특히 암은 다양한 유전자 변이의 축적에 의해 발생하기 때문에, 유전체 변이 패턴을 분석하면 **암의 종류를 예측하거나 특성을 파악하는 데 중요한 단서**를 제공할 수 있습니다.

본 대회는 이러한 **바이오 데이터를 활용한 AI 모델 개발 능력**을 평가하고, 실제 생명과학 문제 해결에 AI 기술을 적용하는 것을 목표로 합니다.

---

# 2️⃣ 문제 정의

주어진 **암환자의 유전체 변이 데이터**를 활용하여  
각 환자가 **26개의 암종(Subclass) 중 어떤 암종에 해당하는지 예측하는 모델**을 개발합니다.

## Task

Multi-class classification

```
Input  : Genomic Variant Data (4384 features)
Output : Cancer Subclass (26 classes)
```

## Evaluation Metric

**Macro F1 Score**

Macro F1은 모든 클래스의 F1 score를 동일한 가중치로 평균하기 때문에  
**클래스 불균형 상황에서도 공정한 평가가 가능합니다.**

---

# 3️⃣ 데이터 설명

## Train Dataset

| 항목 | 설명 |
|-----|-----|
| 샘플 수 | 6201 |
| Feature 수 | 4384 |
| Target | SUBCLASS (26개 암종) |

컬럼 구성

| 컬럼 | 설명 |
|-----|-----|
| ID | 샘플 고유 ID |
| SUBCLASS | 암종 |
| Genomic features | 유전체 변이 정보 |

---

## Test Dataset

| 항목 | 설명 |
|-----|-----|
| 샘플 수 | 2546 |
| Feature 수 | 4384 |

---

# 4️⃣ 접근 전략

이 문제는 다음과 같은 특징을 가지고 있습니다.

## High Dimensional Data

```
Sample : 6201
Feature : 4384
```

Feature 수가 매우 많기 때문에 **차원 저주 문제**가 발생할 수 있습니다.

---

## Sparse Genomic Data

유전체 변이 데이터는 일반적으로 **희소(sparse)** 합니다.

많은 feature가 대부분의 샘플에서 **0 또는 동일한 값**을 가지기 때문에  
의미 있는 feature를 선별하는 과정이 중요합니다.

---

## Multi-class Classification

총 **26개의 클래스**가 존재하며, 일부 클래스는 데이터가 적기 때문에  
**Macro F1 기준 성능 최적화**가 중요합니다.

---

# 5️⃣ 데이터 전처리

다음과 같은 전처리를 수행했습니다.

## 결측값 처리

유전체 변이 데이터는 대부분 **희소 데이터**이기 때문에  
결측값 또는 의미 없는 값들을 확인하고 제거했습니다.

---

## Low Variance Feature 제거

거의 변하지 않는 feature는 모델 학습에 도움이 되지 않기 때문에 제거했습니다.

```
Variance Threshold
```

---

## 희귀 변이 제거

특정 샘플에서만 나타나는 변이는 모델이 과적합할 가능성이 있기 때문에  
빈도가 매우 낮은 feature를 일부 제거했습니다.

---

# 6️⃣ Feature Engineering

유전체 데이터 특성을 고려하여 다음과 같은 feature engineering을 시도했습니다.

---

## Mutation Burden Features

각 샘플에서 발생한 변이 수를 기반으로 다음 feature를 생성했습니다.

- MUTATION_COUNT
- NONZERO_RATIO

암 유형에 따라 **전체 변이량(mutation burden)** 이 다를 수 있다는 가정에 기반합니다.

---

## Gene Group Aggregation

유전자 그룹 단위로 변이를 집계하는 feature를 생성했습니다.

예:

```
DNA Repair genes
Cell Cycle genes
Signal pathway genes
```

개별 변이보다 **생물학적 pathway 단위의 교란 정도**가 암종 분류에 도움이 될 수 있다고 판단했습니다.

---

## Rare Variant Score

희귀 변이의 존재 여부를 반영하는 feature를 생성했습니다.

이는 특정 암종이 **특정 드라이버 변이(driver mutation)** 와 강하게 연관될 가능성을 고려한 것입니다.

---

## External Biological Knowledge

외부 생물학 데이터를 활용하여 다음 정보를 반영하려 시도했습니다.

- cancer related genes
- driver mutation gene list
- pathway level gene groups

하지만 실제 리더보드 성능 향상은 제한적이었습니다.

---

# 7️⃣ 모델링

여러 모델을 실험했습니다.

## Baseline Models

- Logistic Regression
- Random Forest
- XGBoost
- LightGBM
- MLP

---

## Hyperparameter Optimization

Optuna를 사용하여 모델 파라미터를 튜닝했습니다.

```
Optuna + XGBoost
```

---

## 최종 모델

가장 높은 성능을 기록한 모델은

**AutoGluon AutoML Ensemble**

이었습니다.

AutoGluon은 여러 모델을 자동으로 학습하고 앙상블하여  
복잡한 데이터 패턴을 효과적으로 학습했습니다.

---

# 8️⃣ 실험 결과

여러 feature engineering과 변수 선택을 시도했지만  
가장 큰 성능 향상은 **AutoML 기반 앙상블 모델**에서 나타났습니다.

특히 고차원 데이터에서는 단일 모델보다  
**여러 모델의 앙상블이 더 안정적인 성능**을 보였습니다.

---

# 📈 최종 결과

```
Final Rank : 39 / 1827
Metric : Macro F1
```

---

# 9️⃣ 회고

이번 대회를 통해 다음과 같은 점을 배울 수 있었습니다.

## 바이오 데이터의 복잡성

생물학적으로 의미 있는 feature가  
반드시 모델 성능 향상으로 이어지지는 않는다는 것을 경험했습니다.

---

## 고차원 데이터에서 AutoML의 강점

수천 개의 feature가 존재하는 상황에서는  
개별 모델 튜닝보다 **AutoML 기반 앙상블 모델**이 더 강력한 성능을 보였습니다.

---

## Feature Engineering의 한계

도메인 기반 feature engineering을 여러 번 시도했지만  
리더보드 성능 향상은 제한적이었습니다.

이는 유전체 데이터가 **복잡한 상호작용 구조**를 가지고 있기 때문이라고 생각합니다.

---

# 🚀 Future Work

향후 다음과 같은 접근을 시도해볼 수 있습니다.

- Transformer 기반 tabular model
- Graph 기반 유전자 네트워크 모델
- Pathway 기반 representation learning
- SHAP 기반 feature interpretation

---

# 📌 핵심 요약

- 암 유전체 변이 기반 **26-class classification**
- **4384 feature 고차원 바이오 데이터**
- feature engineering / 변수 선택 / 외부 데이터 실험
- **최종 모델: AutoGluon**
- **Final Rank: 39 / 1827**
