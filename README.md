# UrbanCart-AI-Shopping-Assistant
Multilingual (English, Hindi, Hinglish) AI shopping assistant. RAG plus tool-calling with owner-only order data and honest human handoff.
<p align="center">
  <img src="assets/banner.svg" alt="Ask UrbanCart: AI shopping assistant" width="100%"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/RAG-Chroma-E4606D?style=for-the-badge" alt="RAG"/>
  <img src="https://img.shields.io/badge/LangChain-0.3-F2B25B?style=for-the-badge" alt="LangChain"/>
  <img src="https://img.shields.io/badge/Languages-EN%20%C2%B7%20%E0%A4%B9%E0%A4%BF%E0%A4%82%20%C2%B7%20Hinglish-F4A3B5?style=for-the-badge" alt="Languages"/>
  <img src="https://img.shields.io/badge/Status-Working%20prototype-3B1624?style=for-the-badge" alt="Status"/>
</p>

<p align="center">
  <b><a href="#-the-problem">Problem</a></b> ·
  <b><a href="#-the-solution">Solution</a></b> ·
  <b><a href="#-demo">Demo</a></b> ·
  <b><a href="#-results">Results</a></b> ·
  <b><a href="#-whats-next">What's next</a></b> ·
  <b><a href="#-run-it">Run it</a></b>
</p>

---

## ✦ The problem

UrbanCart's support team answers the same questions again and again.

| | |
|---|---|
| **6,000** | customer chats every day |
| **~40%** | repetitive and answerable from documents |
| **44%** | resolved by the existing FAQ bot |
| **46%** | of catalog rows pass basic data rules (26% of prices missing) |

**Objective:** build an assistant that answers from real catalog and policy data, in English, Hindi and Hinglish, and hands off to a human instead of guessing.

## ✦ The solution

<p align="center">
  <img src="assets/flow.svg" alt="Animated flow: customer message to intent router to answer, tools or human handoff, then reply in the same language" width="100%"/>
</p>

- **Grounded answers.** Retrieval over the cleaned catalog and four policy documents, with the source cited on every answer.
- **Tools, not guesses.** Five tools for stock, orders, alternatives, tickets and policy answers. Order details are shown only to the signed-in owner.
- **Three languages.** A guard checks that the reply is in the language the customer wrote in.
- **Honest handoff.** If the data is not there, the assistant says so and offers a human instead of inventing an answer.
- **Cleaned data.** Catalog quality rose from 46% to 94% (verified prices), and duplicate SKUs fell from 154 to 0.

## ✦ Demo

Gradio app, run privately (no public link).

<table>
  <tr>
    <td align="center"><b>English · policy</b><br/><img src="assets/demo-policy-english.png" width="420"/></td>
    <td align="center"><b>Hinglish · policy</b><br/><img src="assets/demo-policy-hinglish.png" width="420"/></td>
  </tr>
  <tr>
    <td align="center"><b>Guest · no customer ID</b><br/><img src="assets/demo-order-guest.png" width="420"/></td>
    <td align="center"><b>Signed in · C1001</b><br/><img src="assets/demo-order-signed-in.png" width="420"/></td>
  </tr>
</table>

The same `handle_query` pipeline powers the Gradio app, the FastAPI endpoint (`POST /ask`) and the offline replay.

## ✦ Results

<p align="center">
  <img src="assets/results.svg" alt="Animated bar chart: old FAQ bot vs Ask UrbanCart by question type" width="100%"/>
</p>

Measured by replaying **300 real chat-log queries** against the old bot and this assistant.

| Metric | Old bot | Ask UrbanCart |
|---|---|---|
| Handled without a human (all 300 queries) | 44.0% | **60.3%** |
| Like for like, in-scope questions only | 50.6% | **54.1%** (+3.5 pts) |
| Human tickets | 78 | **5** |
| Latency, median / P95 | n/a | 1.8 s / 3.5 s |
| Cost per query | n/a | Rs 0.027 |
| Estimated saving | n/a | Rs 9.7 lakh/month vs an all-human baseline (about Rs 61,000/month vs the current bot) |

Quality checks on a 31-question test set: faithfulness 1.00, answer relevancy 0.96, hallucination proxy 0.0%.

### Read these numbers honestly

- Most of the headline gain comes from correctly **refusing off-topic and adversarial messages**. On in-scope questions the improvement is a smaller +3.5 points.
- **Product questions trail the old bot** (32.8% vs 55.2%) because the catalog has no size, colour or material fields.
- Scores of 100% come from small, clean test sets (for example, policy retrieval on 10 questions). Read them as "passes on our tests", not production accuracy.
- Savings assume Rs 300/hour, 5 minutes per query and 40% suitable volume. Orders are mocked.

## ✦ What's next

| # | Move | Why |
|---|---|---|
| 1 | Enrich the catalog (size, colour, material, specs) | 28% of in-scope questions ask for details we do not hold |
| 2 | Order-history lookup for signed-in customers | 71% of order questions arrive without an order ID |
| 3 | Faster, leaner retrieval (top-k 5 to 3, caching) | P95 is 3.5 s against a 3 s target; context precision is 0.16 |
| 4 | Pilot safely | Limited A/B, human review of a sample of answers, a real Order API |

## ✦ Run it

1. Open the notebook (`urbancart_FINAL_submit.ipynb`) in Google Colab and run all cells in order.
2. Add your OpenAI key as a Colab secret named `OPENAI_API_KEY`. **Never paste or commit a key.**
3. The Gradio app starts privately from the last cells. Try customer ID `C1001` for order lookups, or leave it blank as a guest.

```text
.
├── urbancart_FINAL_submit.ipynb   # full pipeline, evaluation and replay
├── assets/                        # README visuals
├── docs/                          # presentation deck
└── README.md
```

## ✦ Security and privacy

- No API keys in the repo. Use environment variables or Colab secrets.
- The Gradio demo is not shared publicly.
- Generated files such as `escalation_queue.json` and `urbancart_replay_results.csv` may contain customer text; keep them out of version control (add them to `.gitignore`).

## ✦ Status

Working prototype with mocked orders, evaluated offline. It is not deployed to production.

<p align="center"><sub>Built as a GenAI project at Learnbay · Palette inspired by Hyphen</sub></p>
