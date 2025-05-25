# HealthMate

!pip install pyngrok flask openai
!pip install flask-ngrok
!pip install flask pyngrok transformers torch
!pip install pyngrok
!pip install transformers
!pip install wikipedia
!pip install openai==0.28

!pip install flask pyngrok wikipedia requests
!pip install matplotlib

from pyngrok import ngrok

ngrok.set_auth_token("2xa39hoKmeyEHxRqFla5tNUJhWm_4BbeZALBGE8kFN2A2M5qW")

html_template = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>HealthMate AI – Your Personal Habit Coach</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            max-width: 750px;
            margin: 30px auto;
            padding: 25px;
            color: #222;
            border-radius: 12px;
            position: relative;
            background: url('https://itbrief.asia/uploads/story/2024/12/11/techday_26169f03bf0a929134f3.webp') no-repeat center center fixed;
            background-size: cover;
            min-height: 100vh;
            box-shadow: 0 0 0 100vw rgba(255,255,255,0.88) inset;
        }

        h1 {
            text-align: center;
            color: #1e90ff;
            margin-bottom: 30px;
            font-size: 32px;
            text-shadow: 1px 1px 1px rgba(0,0,0,0.05);
        }

        .chatbox {
            border: 1px solid #ddd;
            background: #fff;
            border-radius: 10px;
            height: 380px;
            overflow-y: auto;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
            display: flex;
            flex-direction: column;
        }

        .user-msg, .bot-msg {
            margin-bottom: 12px;
            padding: 10px 15px;
            border-radius: 8px;
            max-width: 90%;
        }

        .user-msg {
            background-color: #e6f2ff;
            color: #004080;
            font-weight: bold;
            align-self: flex-end;
            margin-left: auto;
        }

        .bot-msg {
            background-color: #e8f5e9;
            color: #006400;
            white-space: pre-wrap;
            margin-right: auto;
        }

        form {
            display: flex;
            gap: 12px;
            align-items: center;
        }

        input[type="text"] {
            flex: 1;
            padding: 12px 14px;
            font-size: 16px;
            border-radius: 8px;
            border: 1px solid #bbb;
            box-shadow: inset 0 1px 2px rgba(0,0,0,0.05);
            transition: border 0.2s ease;
        }

        input[type="text"]:focus {
            border-color: #1e90ff;
            outline: none;
        }

        input[type="submit"] {
            background-color: #1e90ff;
            color: white;
            border: none;
            border-radius: 8px;
            padding: 12px 20px;
            font-size: 16px;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.1s ease;
        }

        input[type="submit"]:hover {
            background-color: #187bcd;
            transform: scale(1.03);
        }

        .habit-score {
            font-weight: bold;
            font-size: 20px;
            margin: 18px 0;
            color: #1e90ff;
            text-align: center;
        }

        .youtube-links {
            margin-top: 20px;
            padding: 15px;
            background: #fff7f7;
            border-left: 4px solid #ff0000;
            border-radius: 8px;
        }

        .youtube-links h3 {
            margin-top: 0;
            color: #cc0000;
        }

        .youtube-links a {
            display: inline-block;
            margin: 6px 10px 6px 0;
            background-color: #ff0000;
            color: white;
            padding: 8px 14px;
            border-radius: 5px;
            text-decoration: none;
            font-weight: bold;
            font-size: 14px;
            transition: background-color 0.3s ease;
        }

        .youtube-links a:hover {
            background-color: #cc0000;
        }

        .footer {
            margin-top: 40px;
            text-align: center;
            font-size: 14px;
            color: #555;
        }

        canvas {
            margin-top: 20px;
            background-color: #fff;
            padding: 10px;
            border-radius: 10px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
    </style>
</head>
<body>
    <h1>HealthMate AI – Your Personal Habit Coach</h1>
    <div class="chatbox">
        {% if conversation %}
            {% for entry in conversation %}
                <div class="user-msg">You: {{ entry.user }}</div>
                <div class="bot-msg">Coach: {{ entry.bot }}</div>
            {% endfor %}
        {% else %}
            <p>Hi! I am your AI health habit coach. Tell me about your health habits or ask for advice.</p>
        {% endif %}
    </div>

    <form method="POST">
        <input type="text" name="prompt" placeholder="Type your message here..." required autocomplete="off" />
        <input type="submit" value="Send" />
    </form>

    {% if habit_score %}
    <div class="habit-score">Your Habit Score: {{ habit_score }}/100</div>
    {% endif %}

    {% if labels and data %}
    <h2 style="text-align: center; color: #333;">Your Habit Score Trend</h2>
    <canvas id="habitChart" width="100%" height="50"></canvas>
    {% endif %}

    {% if youtube_links %}
    <div class="youtube-links">
        <h3>Recommended Videos:</h3>
        {% for title, link in youtube_links %}
            <a href="{{ link }}" target="_blank" rel="noopener noreferrer">{{ title }}</a>
        {% endfor %}
    </div>
    {% endif %}

    <div class="footer">
        <p>HealthMate AI &copy; 2025</p>
    </div>

    {% if labels and data %}
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        const labels = {{ labels|safe }};
        const data = {
            labels: labels,
            datasets: [{
                label: 'Habit Score',
                backgroundColor: 'rgba(30,144,255,0.2)',
                borderColor: 'rgba(30,144,255,1)',
                data: {{ data|safe }},
                fill: true,
                tension: 0.4
            }]
        };
        const config = {
            type: 'line',
            data: data,
            options: {
                responsive: true,
                scales: {
                    y: {
                        min: 0,
                        max: 100
                    }
                }
            }
        };
        new Chart(document.getElementById('habitChart'), config);
    </script>
    {% endif %}
</body>
</html>
"""
from flask import Flask, request, render_template_string
from pyngrok import ngrok
import requests
import wikipedia
import xml.etree.ElementTree as ET
import matplotlib.pyplot as plt
import os
import datetime

app = Flask(__name__)

# Hugging Face API Token
HF_API_TOKEN = "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"

# Function to get Hugging Face model response
def get_hf_response(prompt, model_name="HuggingFaceH4/zephyr-7b-beta"):
    api_url = f"https://api-inference.huggingface.co/models/{model_name}"
    headers = {
        "Authorization": f"Bearer {HF_API_TOKEN}",
        "Content-Type": "application/json"
    }
    payload = {
        "inputs": f"User: {prompt}\nAssistant:",
        "parameters": {
            "max_new_tokens": 150,
            "temperature": 0.7,
            "do_sample": True
        }
    }

    try:
        response = requests.post(api_url, headers=headers, json=payload)
        response.raise_for_status()
        output = response.json()
        if isinstance(output, list) and 'generated_text' in output[0]:
            return output[0]['generated_text'].split("Assistant:")[-1].strip()
        else:
            return "Sorry, couldn't generate a response."
    except Exception as e:
        return f"Error: {str(e)}"

# Store conversation history
conversation_history = []
habit_score_log = []  # List to store (date, score) tuples

# Calculate a mock habit score
def calculate_habit_score(text):
    score = min(max(len(text) // 2, 10), 100)
    return score

# Recommend YouTube links
def get_youtube_recommendations(text):
    links = []
    text_lower = text.lower()
    if 'exercise' in text_lower:
        links.append(("Best Home Exercises", "https://www.youtube.com/watch?v=UItWltVZZmE"))
    if 'nutrition' in text_lower or 'diet' in text_lower:
        links.append(("Healthy Eating Tips", "https://www.youtube.com/watch?v=6QzGVHHplE0"))
    if 'sleep' in text_lower:
        links.append(("Improve Sleep Quality", "https://www.youtube.com/watch?v=hvxHFDVbdcM"))
    if not links:
        links.append(("General Health Tips", "https://www.youtube.com/watch?v=1hHMwLxN6EM"))
    return links

# Fetch summary from Wikipedia
def get_summary_from_wiki(query):
    try:
        summary = wikipedia.summary(query, sentences=2)
        return summary
    except wikipedia.exceptions.DisambiguationError as e:
        try:
            summary = wikipedia.summary(e.options[0], sentences=2)
            return summary
        except:
            return ""
    except:
        return ""

# Fetch research articles from arXiv
def fetch_arxiv_articles(query="health habits", max_results=5):
    base_url = "http://export.arxiv.org/api/query?"
    search_query = f"all:{query.replace(' ', '+')}"
    url = f"{base_url}search_query={search_query}&start=0&max_results={max_results}"
    response = requests.get(url)
    articles = []
    if response.status_code == 200:
        root = ET.fromstring(response.content)
        for entry in root.findall('{http://www.w3.org/2005/Atom}entry'):
            title = entry.find('{http://www.w3.org/2005/Atom}title')
            link = None
            for l in entry.findall('{http://www.w3.org/2005/Atom}link'):
                if l.attrib.get('type') == 'text/html':
                    link = l.attrib['href']
                    break
            if title is not None and link is not None:
                articles.append((title.text.strip(), link))
    return articles

def plot_habit_progress():
    if not habit_score_log:
        return None
    dates = [entry[0] for entry in habit_score_log]
    scores = [entry[1] for entry in habit_score_log]

    plt.figure(figsize=(6, 4))
    plt.plot(dates, scores, marker='o', color='blue')
    plt.title("Habit Score Progress")
    plt.xlabel("Date")
    plt.ylabel("Habit Score")
    plt.ylim(0, 110)
    plt.grid(True)
    chart_path = "static/habit_score_plot.png"
    os.makedirs("static", exist_ok=True)
    plt.savefig(chart_path)
    plt.close()
    return chart_path

# Main route
@app.route('/', methods=['GET', 'POST'])
def home():
    habit_score = None
    youtube_links = None
    chart_path = None

    if request.method == 'POST':
        user_input = request.form['prompt']
        conversation_history.append({'user': user_input, 'bot': ''})

        # Get Wikipedia info to add context
        wiki_info = get_summary_from_wiki(user_input)
        combined_input = f"{wiki_info}\n{user_input}" if wiki_info else user_input

        # Get model response
        bot_response = get_hf_response(combined_input)
        conversation_history[-1]['bot'] = bot_response

        # Calculate habit score and add to log with timestamp
        habit_score = calculate_habit_score(user_input)
        timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        habit_score_log.append((timestamp, habit_score))

        # Generate/update chart
        chart_path = plot_habit_progress()

        # Get YouTube recommendations
        youtube_links = get_youtube_recommendations(user_input)
    else:
        if not conversation_history:
            conversation_history.append({'user': '', 'bot': 'Hi! I am your AI health habit coach. Tell me about your health habits or ask for advice.'})

    return render_template_string(html_template,

                                  conversation=conversation_history,
                                  habit_score=habit_score,
                                  youtube_links=youtube_links,
                                  chart_path=chart_path)

# Start app with ngrok
if __name__ == "__main__":
    public_url = ngrok.connect(5000)
    print(f" * ngrok tunnel available at: {public_url}")
    app.run(port=5000)
