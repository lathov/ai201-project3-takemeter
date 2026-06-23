# TakeMeter Fine-tuned LLM Classification

## Community
The community selected was the sub-reddit WallStreetBets. Despite a good dataset is provided by Kaggle, the community's conversations is varied in topics and tone.


## Label Taxonomy and Examples

The following labels were selected for classification purposes

- **"warning"**: a post telling the community urgent notifications that an action is needed.<br>
*Example: (1)"To all who is supporting GME( movie land, the brick phone, etc..), we shall go to the next galaxy. Limit sell 1000.", (2)"Consider a company with no assets, income, expenses or costs. Just a shell company."*

- **"supportive"**: a post exposing non-urgent, useful and helpful information for the community.<br>
*Example: (1)"Missed the train and couldn't invest at this point even if I wanted to so just wanna say how amazing it is to watch suits get seriously fucked for once.", (2)"For the past few months, I've been testing a strategy that is based around discussion on WallStreetBets. I don't need to give you an introduction into what's been going on here, so I'll get right into the pretty pictures. Strategy..."*

- **"question"**: a post requesting information or help from the community.<br>
*Example: (1)"Did I fall for a pump and dump is my money gonna be gone by tomorrow?", (2)"wanting to buy tomorrow with a 1K budget, a lot of discord saying $AMC and then all of reddit saying $GME, with that budget whats the move?"*

- **"rant"**: a post using strong languange against someone or something else
*Examples: (1)"Wall Street and the media are showing their real face today and this week. They dont care about anything other than their money. Y'all knows the deets so I'll fuck off but BUY AND HOLD TILL THESE MFERS ARE BACK TO THEIR MOM'S BASEMENT!", (2)"This is a war you waged upon everyone. This isn't reddit, this is you vs. the people. You caused this, you made this. SEC? This is your call to action, you have strength to take them down, GME will not be the only one. You have ruined millions of people's lives it's time we take back what is ours. Down with the institutions, down with MMs. This is our fight and we will finish it, bleed them dry. 
We like the stock (not financial advice)
Edit: Mods thank you being so amazing and keeping WSB around. Over the past 3~ years I've had nothing but enjoyment. Thank you."*

## Labeling

### Data Collection Plan
Samples are provided through Kaggle Dataset. The dataset comprises of 52,049 posts for sub-reddit "WallStreetBets"; and I will try to get a balanced dataset, to avoid labels under-represented.

---

### Labeling Process
The labeling process was performed 98% manual. Gemini Pro, was used to test two posts and their labels. The main challenge to categorize the posts was to differentiate "warning" and "supporting" since they could create some ambiguity, when the sense of urgency could not be properly defined. I will handle as "warning" when it tangible sense of urgency shown in the description.

Some hard examples

1. "I am absolutely furious like everyone else at the brokers locking down trading on stocks they consider "volatile". If someone was cynical they might assume it was to allow the shorters on companies like AMC to get out before they get spanked. Are there any brokers that folk recommend that have not tried to limit access during our moon trip?"

    *This post could be categorized a "rant" or "question". I labeled this as a "question", as the user finalize the post with an actual question.*

2. "Is still allowing purchases of GME. Most others have locked it out. You can still download webull and buy today if you want to. Don't let them stop you"

    *This post starts with a question, but supportive at the same time. I labeled this as supportive, because it seems the user posting partially answered himself. Reason why, it was labeled as "supportive"*

3. "Guys it's simple.  If you're selling YOURE hurting the stock price and a ur fellow stonkers.  If you sell you make less money if you buy you make more. It's literally supply and demand eco 101. For those of you paperweights without the understanding or balls to do it it's really simple."

    *This post could be labeled as a "warning" or "supportive". However, it will be classifed as "supportive" since there is no sense of timing or urgency strictly suggested."
---

### Label Distribution

- Columns: ['title', 'score', 'id', 'url', 'comms_num', 'created', 'text', 'timestamp', 'label']
- Total examples: 201
- Label distribution:

    | label | count | % 
    | -- | --| --
    | supportive | 69 | 34%
    | warning    | 68 | 34%
    | rant       | 34 | 17%
    | question   | 30 | 15%

## Fine-Tuning 

### Fine-Tuning Approach

The LLM model to be fine-tuned is the DistilBERT-base-uncased, which is a light Transformer model trained by knowledge distilling the BERT-base mdel. It is commonly used for Text Classification, like sentiment analysis, spam detection, or topic categorization.

For this particular project, the **epochs** were increased however the model tend to overfit after 3 epochs. A smaller **Learning Rate** of 1e-5 was tried out as well combined with a smaller **batch size** of 8, to increase regularization, and try to find a local-optima, however, that strategy did not work either.


---

## Baseline approach

### Baseline Description

Groq's `llama-3.3-70b-versatile` LLM model was used to implement a Zero Shot Prompt strategy as a baseline. 

----

### Prompt

Below is the prompt implemented.

```python
SYSTEM_PROMPT = """
You are classifying posts from an Investing Community.
Assign each post to exactly one of the following categories.

warning: a post telling the community urgent notifications that an action is needed in a timely basis.
Example: "To all who is supporting GME( movie land, the brick phone, etc..), we shall go to the next galaxy. Limit sell 1000."

supportive: a post exposing non-urgent, useful and helpful information for the community.
Example: "Missed the train and couldn't invest at this point even if I wanted to so just wanna say how amazing it is to watch suits get seriously fucked for once."

question: a post requesting information or help from the community.
Example: "Did I fall for a pump and dump is my money gonna be gone by tomorrow?"

rant: a post using strong languange against someone or something else
Example: "Wall Street and the media are showing their real face today and this week. They dont care about anything other than their money. Y'all knows the deets so I'll fuck off but BUY AND HOLD TILL THESE MFERS ARE BACK TO THEIR MOM'S BASEMENT!"

Respond with ONLY the label name.
Do not explain your reasoning.

Valid labels:
warning
supportive
question
rant  
"""
```
---

### How were the labels collected

Labels were collected from the LLM response. Since the respose could contain additional information based on their temperature, top-p or top-k parameters, the routine matched the actual label, or if the label was contained in the whole string.




## Evaluation


### Fine-Tuning Results

```
🎯 Fine-tuned model accuracy: 0.484

Per-class metrics (fine-tuned model):
              precision    recall  f1-score   support

  supportive       0.47      0.82      0.60        11
     warning       0.50      0.55      0.52        11
        rant       0.00      0.00      0.00         5
    question       0.00      0.00      0.00         4

    accuracy                           0.48        31
   macro avg       0.24      0.34      0.28        31
weighted avg       0.35      0.48      0.40        31
```

### Fine-Tuning Confusion Matrix

```
                       PREDICTED
                sup   wrn   rnt   qst
              ┌───────────────────────┐
   supportive │  9  │  2  │  0  │  0  │
              │───────────────────────│
T  warning    │  5  │  6  │  0  │  0  │
R             │───────────────────────│  
U  rant       │  2  │  3  │  0  │  0  │
E             │───────────────────────│
   question   │  3  │  1  │  0  │  0  │
              └───────────────────────┘
```

### Baseline Results

```
🎯 Baseline accuracy: 0.484  (evaluated on 31/31 parseable responses)

Per-class metrics (baseline):
              precision    recall  f1-score   support

  supportive       0.89      0.73      0.80        11
     warning       0.00      0.00      0.00        11
        rant       0.27      0.80      0.40         5
    question       0.43      0.75      0.55         4

    accuracy                           0.48        31
   macro avg       0.40      0.57      0.44        31
weighted avg       0.41      0.48      0.42        31
```

### Results Comparison

```
==================================================
RESULTS COMPARISON
==================================================
Model                               Accuracy
---------------------------------------------
Zero-shot baseline (Groq)              0.484
Fine-tuned DistilBERT                  0.484
---------------------------------------------

Fine-tuning improvement: 0.000
```

### Wrong Predictions

--- #1 ---<br>
Text:      We have grown to the kind of size we only dreamed of in the time it takes to get a bad nights sleep. We've got so many comments and submissions that we can't possibly even read them all, let alone act...
True:      supportive
Predicted: warning  

    Analysis: Hard to figure out why the model classified it as warning, since no sense of urgency is in the post.

--- #2 ---<br>
Text:      Guys it's simple.  If you're selling YOURE hurting the stock price and a ur fellow stonkers.  If you sell you make less money if you buy you make more. It's literally supply and demand eco 101. For th...
True:      supportive
Predicted: warning  

    Analysis: Here, the LLM probably considered the tone of the post as impossing and not helping

--- #3 ---<br>
Text:      Short tf out of them WSB Army
True:      warning
Predicted: supportive
    
    Analysis: Probably this sentence the way was written does not provide the right context for a good classification

### Sample Classification Table

Sample | Text | True | Predicted | Confidence
-- | -- | -- | -- | -- 
1 | And a hearty F**K Y ** to anyone who sides with billionaire whales crying their soft little cucumber covered eyes out. Let them know they f ** ked up. | rant | warning  | 0.29 |
2 | Ever since the top stocks GME, AMC, BB and NOK took off robinhood doesn't support them anymore. Does anyone have a quick substitute so the rest of us don't miss out on the action? | question | warning | 0.30
3 | Why when I click on NOK, it says at the bottom This stock is not supported on robinhood.? | question | warning | 0.29
4 | Is still allowing purchases of GME. Most others have locked it out. You can still download webull and buy today if you want to. Don't let them stop you | supportive | supportive  | 0.29

For #4 the fine-tuned LLM classified the post correctly with a confidence of 0.29. The post clearly shows a sense of help and support for others in the community

## Reflection

The model did not performed as expected, hitting the same accuracy score compared to the zero-shot baseline. Mostly classifying the "rant" and "question" labels, which the model failed to classify all of them. Recall and F1 scores which really tells the performance of the model predicting the labels out of all true labels were good for supportive giving scores of 0.82 and 0.5 respectively.

In summary, the classification model needs more experimentation, mostly at the labeling stage, despite the dataset is not highly imbalanced, the addition of more samples for "rant" and "question" will help the model to perform much better.

## Spec Reflection

The planning stage help me to structure my strategy about the taxonomy of the labels to be implemented, and how to deal with ambiguity in the definitions.

## AI Usage

### Label Stress Testing
I passed my label taxonomy over to Gemini using the following prompt:

You are a post writer bot for an Investing Community. Create 
6 posts that can be labeled or categorized as follows:

- "warning": a post telling the community urgent notifications that an action is needed.
- "supportive": a post exposing non-urgent, useful and helpful information for the community.
- "question": a post requesting information or help from the community.
- "rant": a post using strong language against someone or something else

The LLM created well defined edge cases.

---

### LLM vs My Annotations

Under the previous context, I asked the LLM to classify some posts from the dataset using the same label taxonomy. It properly classified two examples, one of them was wrongly classify by me. Here is the ouput for that post that I had classified as "supportive"

Post: "For some reason, hundreds of thousands of people seem to think it speaks for this subreddit."

    Classification: Rant

    Reasoning: The author is expressing frustration and annoyance at an external group of people misrepresenting the community. It lacks a direct question, urgent call to action, or helpful advice, placing it squarely in the rant category.





