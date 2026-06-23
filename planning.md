# Planning

## Design

### Community
The community selected was the sub-reddit WallStreetBets. Despite a good dataset is provided by Kaggle, the community's conversations is varied in topics and tone.

---

### Labels

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

---

### Hard Edge Cases
I think "warning" and "supporting" could create some ambiguity, when the sense of urgency could not be properly defined. I will handle as "warning" when it tangible sense of urgency shown in the description

---

### Data Collection PLan
Samples are provided through Kaggle Dataset. The dataset comprises of 52,049 posts for sub-reddit "WallStreetBets"; and I will try to get a balanced dataset, to avoid labels under-represented.

---

### Evaluation Metrics
As accuracy is not a reliable metric, F1 and min(Re,P) scores will be used to evaluate the prediction power of the model over the true labels.

---

### Definition of Success
A F1 and min(Re,P) scores of 0.85 will account for a good performing model.

## Evaluation Plan

As any other classification project, model experimentation and hyper-parameter tunning experimentation and Cross-Validation are needed to determine the best trade-off between bias and variance. Second, the model is not targeting 100% scores, as an optimal threshold needs to be found to deal with ambiguity.

## AI Tools

### Label Stress Testing
I passed my label taxonomy to Gemini using the following prompt:

You are a post writer bot for an Investing Community. Create 
6 posts that can be labeled or categorized as follows:

- "warning": a post telling the community urgent notifications that an action is needed.
- "supportive": a post exposing non-urgent, useful and helpful information for the community.
- "question": a post requesting information or help from the community.
- "rant": a post using strong language against someone or something else

The LLM created well defined edge cases.

### LLM vs My Annotations

Under the previous context, I asked the LLM to classify some posts from the dataset using the same label taxonomy. It properly classified two examples, one of them was wrongly classify by me. Here is the ouput for that post that I had classified as "supportive"

Post: "For some reason, hundreds of thousands of people seem to think it speaks for this subreddit."

    Classification: Rant

    Reasoning: The author is expressing frustration and annoyance at an external group of people misrepresenting the community. It lacks a direct question, urgent call to action, or helpful advice, placing it squarely in the rant category.

