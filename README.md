# Five Questions: a short diabetes screening form

**Aarav Jain**
Nov 15, 2026 

**Track:** Health

**Team Name:** Unsolicited Opinion

## The problem

A community health worker at a screening day has a few minutes per person and no lab. They need to decide who to send for a diabetes blood test, and a 21-question health survey is too long to use at the door. I asked how few questions are enough to make that decision, and who a short form fails.

## What I built

A five-question screening form that suggests whether an adult should get a diabetes check-up.

- **What goes in:** general health (excellent to poor), ever told you have high blood pressure, ever told you have high cholesterol, age, and weight and height (lb or kg, inches or cm).
- **What comes out:** "suggest a diabetes check-up" or "no check-up suggested", a risk score, and a bar chart showing how each answer moved the score.
- **How it is used:** the health worker asks the five questions, enters the answers in the Gradio demo, and refers anyone the tool flags.

This project is a prototype and is not medical advice.

## What I added

My own idea. It is not one of the suggested projects.

- **My own question:** how few questions are needed? I ranked the 21 questions by importance and found that the top five catch as many cases as all 21.
- **Who it fails for:** I compared recall by age, sex and income, found the form misses most cases in people under 40, then tested a fix and measured its cost.
- **Data from a new source:** I ran the model trained on the 2015 survey, unchanged, on 236,378 responses from the 2021 survey.

## How it works

**Data.** The CDC Diabetes Health Indicators dataset: 253,680 responses from adults to a 2015 US health phone survey, with 21 questions each. About 14% report diabetes or pre-diabetes. The data has no missing values and I used it as provided. I held out 20% (50,736 people) as test data the model never saw.

**Model.** I compared three models on all 21 questions: a random forest, XGBoost and logistic regression. The random forest caught only 15% of cases. XGBoost and logistic regression were close (ranking scores, area under the ROC curve, of 0.827 and 0.820), so I chose logistic regression, because I can show exactly how each answer changes the score. The rarer diabetes group is weighted up so the model does not ignore it.

**Choosing the questions.** I ranked the 21 questions by the size of their weights in the logistic regression, then trained new models on the top 3, 5, 7 and 9. I also tested 12 lifestyle questions that need no past check-up.

## Results

Five questions catch as many diabetes cases as all 21: 76% of cases on held-out test data, against 0% for the baseline. The five are general health, body mass index, age, high blood pressure and high cholesterol.

**Baseline.** Labelling everyone "no diabetes" is 86.1% accurate and catches none of the 7,069 cases in the test set. My five-question model is 72% accurate, lower than the baseline, because it trades accuracy to catch three in four cases. That is why I report missed cases and false alarms instead of accuracy.

| Questions asked | Recall | Precision | Missed cases (of 7,069) | False alarms (of 43,667) |
| --- | --- | --- | --- | --- |
| All 21 | 76.1% | 31.1% | 1,689 | 11,933 |
| 12 lifestyle questions (no past check-up needed) | 73.8% | 29.7% | 1,849 | 12,342 |
| Top 3 | 73.4% | 29.4% | 1,878 | 12,475 |
| **Top 5 ** | **76.1%** | **30.3%** | **1,691** | **12,372** |
| Top 9 | 76.5% | 30.9% | 1,661 | 12,117 |

Going from five questions to nine or 21 mainly removes a few hundred false alarms and catches almost no extra cases.

**On new data.** Run unchanged on the 2021 survey, the five-question model had 73.7% recall and 30.6% precision, so it held up six years later. A different author cleaned the 2021 file, which may explain part of the two-point drop.

**Where it fails, and why I think so**

- **People under 40.** The form catches only 37% of their cases (96 of 258), against 84% for people aged 70 and over. Age is one of the five questions, so young people rarely reach the cut-off.
  - **My fix and its cost.** I lowered the cut-off for ages 18 to 39 from 0.50 to 0.25, chosen on a validation slice of the training data. On the test set, their recall rose from 37% to 66%, and their false alarm rate rose from 4% to 16%. Overall that is 74 more cases caught for 881 more false alarms, about 12 for each extra case.
- **Income groups.** Recall falls from 86% in the lower income bands to 66% in the higher ones.
- **Men.** Recall is 74% for men and 78% for women.
- **Most flags are false alarms.** About seven in ten people flagged do not have diabetes, which is expected when screening for a condition 14% of people have.

**Limits.** The survey covers adults only, so the form is not valid for anyone under 18. Answers are self-reported, and everyone surveyed already knew their diagnosis, so the form may work less well on undiagnosed people. Two of the five questions need a past check-up. The risk score is not a percentage chance of having diabetes.

## What I'd do next

- **Test on undiagnosed people.** Find data where diabetes was confirmed by a blood test after the questions were answered, since that is who screening is for.
- **Build a no-check-up form.** Choose the best five questions from the 12 that need no medical history, for people who have never seen a doctor, and try it with a real health worker.

## Sources

**Datasets**

- [CDC Diabetes Health Indicators](https://archive.ics.uci.edu/dataset/891/cdc+diabetes+health+indicators), UCI Machine Learning Repository (2015 survey, my main data). License: [CC0: Public Domain](https://creativecommons.org/publicdomain/zero/1.0/)
- [Diabetes Health Indicators Dataset, 2021 survey](https://www.kaggle.com/datasets/julnazz/diabetes-health-indicators-dataset), Kaggle, by user julnazz (file: diabetes\_binary\_health\_indicators\_BRFSS2021.csv). License: [CC0: Public Domain](https://creativecommons.org/publicdomain/zero/1.0/)
- Both come from the CDC's Behavioral Risk Factor Surveillance System survey.

**Tools and libraries**

- [Google Colab](https://colab.research.google.com), pandas, [scikit-learn](https://scikit-learn.org), [XGBoost](https://xgboost.readthedocs.io), matplotlib, [Gradio](https://www.gradio.app), ucimlrepo, kagglehub

**AI tools**

- ChatGPT: used to plan the project, draft code, and help interpret results.

**Links**

- Code: [notebook on GitHub](https://github.com/skanrrking/DiabetesPrediction/blob/main/Logistic_Regression_Diabetes_Checkup_Predictor.ipynb), or [open it in Colab](https://colab.research.google.com/github/skanrrking/DiabetesPrediction/blob/main/Logistic_Regression_Diabetes_Checkup_Predictor.ipynb)
- Demo video:
