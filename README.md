# Website-Data-Analysis-Using-Python

Following are the objectives of this Analysis:-
1) What patterns or trends can you observe in website sessions and users over
time?
2) Which marketing channel brought the highest number of users to the website,
and how can we use this insight to improve traffic from other sources?
3) Which channel has the highest average engagement time, and what does that
tell us about user behavior and content effectiveness?
4) How does engagement rate vary across different traffic channels?
5) Which channels are driving more engaged sessions compared to non-engaged
ones, and what strategies can improve engagement in underperforming
channels?
6) At what hours of the day does each channel drive the most traffic?
7) Is there any correlation between high traffic (sessions) and high engagement
rate over time?

## Step 1 - Importing Libraries and file

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv("data.csv")

df.head()
```
## Step 2 - Data Cleaning and Preprocessing
```python
df.columns = df.iloc[0]
df = df.drop(index = 0).reset_index(drop = True)
df.columns = ["channel_group", "DateHour", "Users", "Sessions", "Engaged Session","Average engagement time per session"	,"Engaged sessions per user","Events per session","Engagement rate","Event count"]

df.info()
df["DateHour"] = pd.to_datetime(df["DateHour"], format="%Y%m%d%H", errors='coerce')
numeric_cols = df.columns.drop(["channel_group", "DateHour"])
df[numeric_cols] = df[numeric_cols].apply(pd.to_numeric, errors='coerce')
df["Hour"] = df["DateHour"].dt.hour
```
## EDA
```python
df.describe()
```
### 1. Patterns or Trends in Website Sessions and Users Over Time
```python
plt.figure(figsize=(10,5))
df.groupby("DateHour")[["Sessions","Users"]].sum().plot(ax=plt.gca())
plt.title("Sessions and users over time")
plt.xlabel("DateHour")
plt.ylabel("count")
plt.show()
```
<img width="854" height="505" alt="image" src="https://github.com/user-attachments/assets/8c1b8d62-efb8-4e13-8cbb-51ef57383c41" />

* Both sessions and users showed a consistent upward trend over time, indicating growing website traffic and awareness.


* Occasional peaks suggest campaign launches or social media pushes likely influenced traffic.


* The trend lines for users and sessions move almost in parallel, which means user acquisition and repeat visits are increasing proportionally.



### 2. Top Marketing Channel by User Acquisition
```python
plt.figure(figsize=(8, 5))
sns.barplot(data=df, x="channel_group", y="Users", estimator=np.sum, palette="viridis")
plt.title("Total Users by Channel")
plt.xticks(rotation=45)
plt.show()
```
<img width="716" height="544" alt="image" src="https://github.com/user-attachments/assets/3201a08e-9337-429c-93d8-48088683c78b" />

* The Direct channel brought in the highest number of users overall, suggesting strong brand recall and return visitors.


* Organic Social was the next major contributor, showing effective unpaid reach from social platforms.


#### Actionable Insight:


* To improve traffic from other sources, replicate Direct channel success by enhancing brand awareness campaigns and cross-channel consistency.


* Invest in search engine optimization (SEO) and referral partnerships to balance traffic sources.





### 3. Channel with the Highest Average Engagement Time
```python

plt.figure(figsize=(8, 5))
sns.barplot(data=df, x="channel_group",y = "Average engagement time per session",estimator=np.mean,palette='pastel') 
plt.title("Avg Engagement Time by Channel")
plt.xticks(rotation=45)
plt.show()
```
<img width="699" height="544" alt="image" src="https://github.com/user-attachments/assets/854b7dbc-91bf-4452-9f09-b911210e2097" />

* Direct channel also recorded the highest average engagement time per session.


* This indicates that users from Direct traffic are highly motivated and engaged, likely familiar with your content or product.


#### Interpretation:


* The content and UX are particularly effective for returning/loyal users.


* This could guide content personalization strategies to extend engagement across channels.





### 4. Variation of Engagement Rate Across Channels
```python
plt.figure(figsize=(8, 5))
sns.boxplot(data=df, x="channel_group", y="Engagement rate", palette="pastel")
plt.title("Engagement Rate Distribution by Channel")
plt.xticks(rotation=45)
plt.show()
```
<img width="695" height="544" alt="image" src="https://github.com/user-attachments/assets/247b8ece-c098-45d5-95b4-f5c14f7e0030" />

* The Engagement Rate varied moderately among channels:


* Organic Social had a slightly higher engagement rate, suggesting interactive or visually engaging content.


* Paid channels (if present) likely showed lower engagement due to more top-of-funnel visitors.




#### Actionable Insight:


* Optimize underperforming channels with content formats and topics that mirror those used in Organic Social.





### 5. Engaged vs. Non-Engaged Sessions by Channel
```python
# Group and prepare the session summary
session_df = df.groupby("channel_group")[["Sessions", "Engaged Session"]].sum().reset_index()

# Create Non-Engaged column
session_df["Non-Engaged"] = session_df["Sessions"] - session_df["Engaged Session"]

# Optional: clean column names
session_df.columns = session_df.columns.str.strip().str.lower().str.replace(" ", "_")

# Melt the dataframe
session_df_melted = session_df.melt(
    id_vars=["channel_group"],
    value_vars=["engaged_session", "non-engaged"],
    var_name="session_type",
    value_name="count"
)

# Plot
plt.figure(figsize=(8, 5))
sns.barplot(data=session_df_melted, x="channel_group", y="count", hue="session_type", palette="Set2")
plt.title("Engaged vs Non-Engaged Sessions")
plt.xticks(rotation=45)
plt.show()
```
<img width="716" height="544" alt="image" src="https://github.com/user-attachments/assets/00e21b1a-5c59-4759-b67c-7497b53cc270" />

* Across channels, Engaged Sessions were notably higher in Direct and Organic Social sources.


* Non-Engaged Sessions were more frequent in other channels, implying less relevant targeting or user intent mismatch.


* Strategies to Improve Engagement:


* Implement retargeting for bounce-prone channels.


* * Focus on landing page optimization and clear CTAs for ad-driven traffic.


* A/B test content types across channels.


### 6. Hourly Traffic Distribution by Channel
```python

heatmap_data = df.groupby(["Hour", "channel_group"])["Sessions"].sum().unstack().fillna(0)
plt.figure(figsize=(12, 6))
sns.heatmap(heatmap_data, cmap="YlGnBu", linewidths=.5, annot=True, fmt='.0f')
plt.title("Traffic by Hour and Channel")
plt.xlabel("Channel Group")
plt.ylabel("Hour of Day")
plt.show()
```
<img width="928" height="550" alt="image" src="https://github.com/user-attachments/assets/d5ff3e6f-627e-4cc3-9548-7c85779cc584" />

The heatmap showed clear hourly peaks:


* Direct traffic was highest during late evening hours (around 21:00–23:00), aligning with user free time.


* Organic Social peaked during evening hours (18:00–20:00), consistent with social media browsing patterns.




#### Actionable Insight:


* Schedule social media posts and email campaigns during these peak hours for maximum reach.


* Consider time-based personalization for content releases.





### 7. Correlation Between High Traffic and Engagement Rate Over Time
```python
df_plot = df.groupby("DateHour")[["Engagement rate", "Sessions"]].mean().reset_index()
plt.figure(figsize=(10, 5))
plt.plot(df_plot["DateHour"], df_plot["Engagement rate"], label="Engagement rate")
plt.plot(df_plot["DateHour"], df_plot["Sessions"], label="Sessions", color="blue")
plt.title(" Engagement Rate vs Sessions Over Time")
plt.xlabel("DateHour")
plt.legend()
plt.grid(True)
plt.show()
```
<img width="863" height="473" alt="image" src="https://github.com/user-attachments/assets/efdedd1e-f7d4-472c-9c66-598dc0131ff3" />

* The combined plot of Engagement Rate vs. Sessions over time indicates a mild positive correlation — as sessions increase, engagement rate also tends to rise slightly.


* This suggests that traffic growth is not diluting engagement quality, a strong indicator of effective audience targeting.


#### Actionable Insight:


* Continue scaling acquisition while maintaining engagement tactics.


* Monitor for any future divergence, as high traffic with low engagement can signal content fatigue or relevance issues.





### 📊 Overall Summary
Your data reveals a healthy digital performance profile:


* Strong Direct and Organic Social channels are driving both traffic and engagement.


* Engagement metrics align positively with traffic growth.


* There is a clear opportunity to replicate top-channel tactics (especially from Direct) across weaker channels, while optimizing for time-of-day behavior.

