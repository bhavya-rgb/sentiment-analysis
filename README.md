# sentiment-analysis
import streamlit as st
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime

# -----------------------------
# Sample travel posts data
# -----------------------------
posts = [
    {"id": 1, "text": "Amazing flight experience! The crew was incredibly helpful and the food was delicious ✈️😊",
     "sentiment": "Positive", "score": 3, "platform": "Twitter", "timestamp": "2024-08-21 14:30",
     "keywords": ["flight", "crew", "food"]},
    {"id": 2, "text": "Flight delayed 4 hours with no communication from airline staff. Absolutely terrible service 😡",
     "sentiment": "Negative", "score": 4, "platform": "Twitter", "timestamp": "2024-08-21 15:45",
     "keywords": ["flight", "delayed", "service"]},
    {"id": 3, "text": "Hotel room was clean and comfortable. Great location near the beach 🏨",
     "sentiment": "Positive", "score": 2, "platform": "Facebook", "timestamp": "2024-08-21 16:20",
     "keywords": ["hotel", "room", "location"]},
    {"id": 4, "text": "Lost my luggage and customer service is not helping at all. Worst vacation start ever 😞",
     "sentiment": "Negative", "score": 3, "platform": "Twitter", "timestamp": "2024-08-21 17:15",
     "keywords": ["luggage", "service"]},
    {"id": 5, "text": "Beautiful sunset view from the airplane window. Perfect flying weather today 🌅",
     "sentiment": "Positive", "score": 2, "platform": "Instagram", "timestamp": "2024-08-21 18:00",
     "keywords": ["airplane", "weather"]},
    {"id": 6, "text": "Airport security lines are moving slowly today. Standard experience nothing special",
     "sentiment": "Neutral", "score": 0, "platform": "Twitter", "timestamp": "2024-08-21 19:30",
     "keywords": ["airport", "security"]},
]

# -----------------------------
# Calculate sentiment stats
# -----------------------------
df = pd.DataFrame(posts)
sentiment_stats = df["sentiment"].value_counts().to_dict()
total_posts = len(posts)

positive_pct = round((sentiment_stats.get("Positive", 0) / total_posts) * 100, 1)
negative_pct = round((sentiment_stats.get("Negative", 0) / total_posts) * 100, 1)
neutral_pct = round((sentiment_stats.get("Neutral", 0) / total_posts) * 100, 1)

# -----------------------------
# Streamlit UI
# -----------------------------
st.title("🛫 Jatayuv AI Sentiment Monitor")
st.write("Real-time social media sentiment analysis for travel experiences")

# Summary stats
col1, col2, col3, col4 = st.columns(4)
col1.metric("Positive", f"{positive_pct}%", f"{sentiment_stats.get('Positive',0)} posts")
col2.metric("Negative", f"{negative_pct}%", f"{sentiment_stats.get('Negative',0)} posts")
col3.metric("Neutral", f"{neutral_pct}%", f"{sentiment_stats.get('Neutral',0)} posts")
col4.metric("Total Posts", total_posts)

# Alert banner if negative > 30%
if negative_pct >= 30:
    st.error(f"⚠️ High Negative Sentiment Alert! {negative_pct}% of recent posts are negative.")

# -----------------------------
# Charts
# -----------------------------
st.subheader("📊 Sentiment Distribution")

fig, ax = plt.subplots()
df["sentiment"].value_counts().plot(kind="bar", ax=ax, color=["green", "red", "gray"])
st.pyplot(fig)

st.subheader("🥧 Sentiment Breakdown")
fig2, ax2 = plt.subplots()
df["sentiment"].value_counts().plot(kind="pie", autopct="%1.1f%%", ax=ax2, colors=["green", "red", "gray"])
ax2.set_ylabel("")
st.pyplot(fig2)

# -----------------------------
# Posts section
# -----------------------------
st.subheader("📱 Recent Social Media Posts")
for post in posts:
    with st.expander(f"{post['sentiment']} | {post['platform']} | {post['timestamp']}"):
        st.write(post["text"])
        st.write("**Keywords:**", ", ".join(post["keywords"]))

        # Auto-response for negative posts
        if post["sentiment"] == "Negative":
            if st.button(f"🤖 Auto-Respond to Post {post['id']}"):
                st.info("AI Response: We're sorry you had this experience. Our support team will assist you shortly.")

# -----------------------------
# Chat Simulator
# -----------------------------
st.subheader("🤖 AI Response Simulator")
if "chat" not in st.session_state:
    st.session_state.chat = []

user_input = st.text_input("Type your travel feedback...")

if st.button("Send"):
    if user_input.strip():
        st.session_state.chat.append(("user", user_input))
        st.session_state.chat.append(("bot", "Thank you for your feedback! Our AI will analyze and assist you."))

for sender, msg in st.session_state.chat:
    if sender == "user":
        st.markdown(f"🧑 **You:** {msg}")
    else:
        st.markdown(f"🤖 **AI:** {msg}")

st.markdown("---")
st.caption("🚀 Jatayuv AI Sentiment Analysis Tool | Built for Travel Industry Intelligence")


