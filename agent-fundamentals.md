# 🤖 AI Agent Fundamentals

> Everything you need to understand AI agents — explained in simple English with real examples.

---

## 🎯 The Story: Why Do We Need AI Agents?

### Meet Brian — A Developer Tired of Job Hunting

Brian spends 15+ hours every week manually searching jobs, tailoring resumes, and writing cover letters — one by one. With a Job Application Agent, he gives one goal and the agent searches 5 platforms, filters matches, tailors his resume for each, and delivers ready-to-submit applications in 3 minutes.

Now let's understand **how** this works under the hood. 👇

---

## 1. An Agent Needs an LLM

The LLM is the agent's **brain** — it reasons, decides, and generates responses. Without an LLM, an agent can't think or act.

---

## 2. How an Agent Decides What to Do (ReAct Pattern)

```
Goal → Observe → Think → Act → Repeat
```

1. **Goal** — given a task/objective
2. **Observe** — looks at current context
3. **Think** — LLM reasons about what step to take next
4. **Act** — picks a tool or generates output
5. **Repeat** — checks if goal is met; if not, loops back

> 💡 **Real-world analogy:** Rahul wants to apply for a job. He *observes* (reads the job description), *thinks* (do my skills match?), *acts* (tailors resume), *observes* (cover letter needed too), *thinks* (what should I highlight?), *acts* (writes cover letter). That's the ReAct loop.

---

## 3. Core Components of an Agent

| Component | Purpose | Example |
|-----------|---------|---------|
| **Role** | Tells the agent *who* it is | "Expert tech recruiter and resume writer" |
| **Goal** | Tells it *what* to achieve | "Find 5 matching Python jobs and tailor resume for each" |
| **Backstory** | Gives personality/expertise | "10 years in tech hiring, knows what ATS systems look for" |
| **Task** | Tells it *specifically* what to do now | "Search LinkedIn for Senior Python roles in Bangalore" |
| **Tools** | Lets it interact with the real world | Job search API, resume parser, file writer |
| **LLM** | The brain that processes everything | GPT-4, Claude, Llama |

---

## 4. How It All Becomes a Prompt

The framework combines everything into one prompt sent to the LLM:

```
[Role + Goal + Backstory] + [Task + Expected Output] + [User Input] + [Tool Descriptions]
```

You're **programming the agent's behavior through natural language**.

---

## 5. Goal — The Compass

- Guides every decision the agent makes
- Without a goal, agent doesn't know when to stop
- **More specific = better output**

| ❌ Vague | ✅ Specific |
|----------|------------|
| "Help me find a job" | "Find Senior Python roles in Bangalore, 20-35 LPA, remote-friendly, no early-stage startups" |
| "Improve my resume" | "Tailor my resume for this Flipkart job posting, highlight distributed systems and AWS experience" |

Same agent + same tools + **different goal** = completely different output.

---

## 6. Tools — The Hands

- LLMs can only think/generate text by default
- Tools let agents **do things**: search web, read files, call APIs, run code
- Agent receives tool descriptions → decides when to use them → framework executes → result fed back to LLM

> 💡 **Analogy:** An LLM without tools is like a brilliant career advisor locked in a room — they can give you advice about job hunting, but can't actually search job boards, submit applications, or tailor your resume.

---

## 7. Backstory — The Personality

Shapes *how* the agent approaches work:

| Backstory | Behavior |
|-----------|----------|
| "Seasoned tech recruiter" | Knows what hiring managers look for, optimizes for ATS |
| "Meticulous resume writer" | Detailed, keyword-rich, perfectly formatted |
| "Aggressive job hunter" | Applies to everything that's even a 60% match |
| No backstory | Generic, misses important details |

Same goal + **different backstory** = different tone and depth.

---

## 8. Key Takeaway

```
AI Agent = LLM + Role + Goal + Backstory + Task + Tools
```

All working together in a **reasoning loop** to accomplish objectives autonomously.

---

## 9. RAG (Retrieval Augmented Generation)

**RAG ≠ training.** You're NOT training the LLM with your data. You're giving it a **cheat sheet** at query time.

### Why RAG over training?

- Documents change often — RAG picks up changes instantly
- No expensive GPU training needed
- You control exactly what the LLM sees
- LLM stays general-purpose, your data stays private

### RAG Step by Step

```
Ingest → Embed → Store → Query → Retrieve → Generate
```

1. **Ingest** — Take your documents (PDFs, docs, websites), split into small chunks
2. **Embed** — Convert each chunk into a vector (numbers that represent meaning)
3. **Store** — Save vectors in a vector database (Pinecone, OpenSearch, FAISS)
4. **Query** — When user asks something, convert the question into a vector too
5. **Retrieve** — Find the most similar chunks from the database
6. **Generate** — Send those chunks + the question to the LLM as context

### Example

> You have 500 pages of company HR policy.
> User asks: "How many vacation days do I get?"
> RAG finds the relevant paragraph → LLM answers using that specific paragraph — not guessing.

### In Agentic AI

RAG becomes a **tool**. The agent decides "I need company-specific info" → calls the RAG tool → gets relevant docs → uses them to reason.

### RAG vs MCP

| | RAG | MCP |
|--|-----|-----|
| What | Retrieve knowledge | Standardized protocol |
| About | Giving LLM **information** | Giving LLM **ability to do things** |
| Overlap | An MCP tool can be a RAG system underneath |

---

## 10. Memory — Stateless vs Stateful

- **Stateless** — Agent forgets everything after each run. Like talking to a stranger each time.
- **Stateful** — Agent remembers previous interactions.

### Types of Memory

| Type | What it remembers | Example |
|------|-------------------|---------|
| Short-term | Current conversation | "User asked about London earlier in this chat" |
| Long-term | Across sessions | "This user always prefers luxury travel" |
| Entity memory | Facts about specific things | "John = AI Engineer, lives in San Francisco" |

### Storage on AWS (Production)

| Memory Type | AWS Service | Why |
|-------------|-------------|-----|
| Short-term (conversation) | DynamoDB | Fast key-value lookups, serverless |
| Long-term (semantic) | OpenSearch / Bedrock Knowledge Bases | Vector search for similar past interactions |
| Entity memory (facts) | DynamoDB | Store structured facts about users/entities |
| Session history | ElastiCache (Redis) | Fast, temporary, auto-expires |
| Document storage | S3 | Raw files, conversation logs |

---

## 11. Multi-Agent Collaboration

### Pattern 1: Sequential (Most Popular)

```
Agent A → output → Agent B → output → Agent C
```

- Like a pipeline/assembly line
- Simple, predictable, easy to debug
- Example: Job Searcher → Resume Tailor → Application Submitter

### Pattern 2: Hierarchical

```
        Manager Agent
       /      |      \
  Venue    Catering   Music
  Agent     Agent     Agent
```

- A "manager" agent delegates tasks to workers
- Manager decides who does what, combines results

### Pattern 3: Parallel

```
  Agent A ──┐
  Agent B ──┼── Combined Result
  Agent C ──┘
```

- Multiple agents work simultaneously on independent subtasks
- Faster, but tasks must NOT depend on each other

**Sequential is most popular** because it's simplest to build, debug, and trace errors.

---

## 12. MCP (Model Context Protocol)

**MCP = USB for AI tools.**

A universal standard to connect any agent to any tool. Build a tool once, use it with any MCP-compatible agent.

- Agent = MCP **client**
- Tool service = MCP **server**

> See [mcp-notes.md](./mcp-notes.md) for detailed notes.

---

## 13. Cost & Tokens

### What are tokens?

**1 token ≈ ¾ of a word.** 1000 tokens ≈ 750 words.

### Cost formula

```
Cost = (input tokens × input price) + (output tokens × output price)
```

### Why agents are expensive

| | Chatbot | Agent |
|--|---------|-------|
| LLM calls per task | 1 | 5-20+ (ReAct loop) |
| Cost multiplier | 1x | 5-20x |

Every call to LLM = costs money. Tool calls themselves are free.

### Top optimization strategies

1. ⭐ Use the right model for the job (cheap for simple, expensive for complex)
2. ⭐ Keep prompts concise
3. ⭐ Limit agent iterations (set max loops)
4. Cache repeated results
5. Filter tool outputs — only pass relevant parts to LLM
6. Use smaller models for routing decisions

**Biggest wins:** Right model + concise prompts + limiting iterations = **50-70% cost reduction**.

---

## 14. Topics for Next Session

- [ ] Guardrails & Safety
- [ ] Embeddings & Vectors (how RAG works under the hood)
- [ ] Agent Evaluation & Observability

---

## 15. ChatGPT vs AI Agents — What's the Difference?

This is the most common question. Here's the simple answer:

### ChatGPT (plain chatbot)

- You ask → it answers. That's it.
- It does **ONE thing** per message: generate text.
- It can't go do stuff on its own.
- It's **reactive** — waits for you to type something.

### AI Agent

- You give it a goal → it **figures out the steps** → does them → comes back with results.
- It can use **multiple tools**: search, read files, call APIs, write code, execute it.
- It makes decisions in a **loop** (ReAct): think → act → observe → think again.
- It's **proactive** — takes initiative to complete the goal.

### The Analogy

| | ChatGPT | AI Agent |
|--|---------|----------|
| Like... | A career advisor who can only talk | A personal assistant who actually searches, applies, and follows up |
| You say "help me find a job" | Gives you tips about job hunting | Actually searches job boards, tailors your resume, submits applications |

### Real Example

**You:** "Find me Senior Python jobs in Bangalore, remote-friendly"

| ChatGPT | AI Agent |
|---------|----------|
| "Here are some tips: update your LinkedIn, use keywords, try Naukri and LinkedIn..." | *searches 5 job boards* → *finds 47 matches* → *filters to 12* → *tailors resume for top 5* → "Here are your best matches with tailored resumes ready to submit" |

### The Key Difference

> **ChatGPT *talks about* doing things. Agents *actually do* things.**

---

## 16. Error Handling & Retries

Things will go wrong. Here's how to handle it.

### Common Failures

- 🔴 Tool returns an error (API down, rate limited)
- 🔴 LLM gives garbage output (malformed JSON, hallucinated tool name)
- 🔴 Agent gets stuck in a loop (keeps trying the same failing action)

### How to Handle

| Strategy | What it does |
|----------|-------------|
| Max retries | Try a tool 2-3 times, then move on |
| Fallback tools | Google fails? Try Bing. API fails? Try scraping. |
| Output validation | Check LLM output format before using it |
| Max iterations | Hard limit on ReAct loop (e.g., 10 steps max) |
| Graceful degradation | Return partial results with a note about what failed |

### Example

```
Agent tries to search LinkedIn jobs → API returns 429 (rate limited)

❌ Without error handling: agent crashes or loops forever

✅ With error handling: 
   → retries after 5 seconds → still rate limited 
   → switches to Naukri API instead → finds jobs there
   → tells user "LinkedIn was rate limited, used Naukri instead"
```

---

## 17. Human-in-the-Loop

Not everything should be fully autonomous. Sometimes the agent should **pause and ask**.

### When to Pause

- 💰 Spending money (booking, purchasing)
- 🗑️ Deleting data
- 📧 Sending messages to other people
- 🤔 When confidence is low
- ⚠️ Irreversible actions

### Example

```
Agent found a job and is about to submit application...

❌ Without HITL: Submits application with wrong salary expectation. Oops.

✅ With HITL: "I found a great match at Razorpay — Senior Python Engineer, 28 LPA, remote.
   Here's the tailored resume. Should I submit?"
   → User reviews → says yes → agent submits
```

---

## 18. Prompt Engineering for Agents

Writing prompts for agents ≠ chatbot prompting. You're **programming behavior**, not asking questions.

### Key Principles

1. **Be specific in the Role** — "Senior travel researcher with 10 years experience" > "travel helper"
2. **Quantify the Goal** — "Find 3 options under $5000" > "find cheap options"
3. **Constrain the Backstory** — Tell it what NOT to do
4. **Define Expected Output** — "Return a markdown table with columns: X, Y, Z"
5. **Give examples** — Show what good output looks like

### Bad vs Good

```
❌ BAD:
Role: Job helper
Goal: Help with job applications
```

```
✅ GOOD:
Role: Expert tech recruiter with 10 years experience in Indian IT hiring
Goal: Find exactly 5 Senior Python roles in Bangalore, 20-35 LPA, remote-friendly,
      from companies older than 3 years, and tailor resume for each
Backstory: You've placed 500+ candidates at top tech companies. You know exactly 
           what ATS systems filter for. You never apply to jobs below 70% skill match.
Expected Output: Table with: Company, Role, Salary range, Match %, 
                 Key skills to highlight, tailored resume filename
```

The second prompt produces **dramatically better results** with the same LLM and tools.

---

## 19. Deployment Patterns

How do you actually run agents in production?

| Pattern | Best For | Limitation |
|---------|----------|-----------|
| **Serverless (Lambda)** | Simple agents, quick tasks (<15 min) | 15-min timeout |
| **Containers (ECS/AgentCore)** | Complex agents, long workflows | More expensive at low traffic |
| **Hybrid** | Best of both worlds | More complex to set up |

### Example

| Agent | Deploy On | Why |
|-------|-----------|-----|
| Resume tailor | Lambda | Quick task, <30 seconds |
| Full job application agent | ECS/AgentCore | Multiple searches, might take minutes |

**Key rule:** Agents make multiple LLM calls. If your ReAct loop takes >15 min, you need containers.

---

## 20. Testing Agents

Agents are hard to test because LLM outputs are **non-deterministic**. Same input ≠ same output.

### What to Test

| Test Type | What You're Checking |
|-----------|---------------------|
| Tool tests | Does each tool work correctly? |
| Output format | Does agent return expected structure? |
| Behavior tests | Does agent take reasonable steps? |
| Guardrail tests | Does agent refuse bad requests? |
| Cost tests | Does agent stay within token limits? |

### How to Test

- **Mock the LLM** — Return fixed responses to test orchestration logic
- **Record & replay** — Record a successful run, replay for regressions
- **Evaluation datasets** — 20-50 test cases with expected outcomes
- **LLM-as-judge** — Use another LLM to score output quality

### Example

```python
def test_agent_uses_search():
    result = agent.run("Find Senior Python jobs in Bangalore")
    assert "search" in [step.tool_name for step in result.steps]
    assert "python" in result.output.lower()
```

**Reality:** Most teams start with manual testing. Add automated tests as the agent stabilizes.

---

## 🗺️ What's Next?

- Guardrails & Safety
- Embeddings & Vectors
- Agent Evaluation & Observability
- Building a full agent project from scratch

---

*Built while learning. Contributions welcome!* ⭐
