# 🤖 AI Agent Fundamentals

> Everything you need to understand AI agents — explained in simple English with real examples.

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

> 💡 **Real-world analogy:** You want coffee. You *observe* (kitchen is empty), *think* (need to go to café), *act* (walk to café), *observe* (long line), *think* (try another café), *act* (go next door). That's the ReAct loop.

---

## 3. Core Components of an Agent

| Component | Purpose | Example |
|-----------|---------|---------|
| **Role** | Tells the agent *who* it is | "Senior travel consultant" |
| **Goal** | Tells it *what* to achieve | "Find 3 beach resorts under $3000/week" |
| **Backstory** | Gives personality/expertise | "Visited 200+ resorts, hates touristy places" |
| **Task** | Tells it *specifically* what to do now | "Search for Bali villas with private pools" |
| **Tools** | Lets it interact with the real world | Search API, booking API, calculator |
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
| "Research vacation spots" | "Find luxury 5-star spots with private beaches, under $5000/week" |
| "Help with resume" | "Tailor this resume for a Senior Python role at Netflix, highlighting distributed systems experience" |

Same agent + same tools + **different goal** = completely different output.

---

## 6. Tools — The Hands

- LLMs can only think/generate text by default
- Tools let agents **do things**: search web, read files, call APIs, run code
- Agent receives tool descriptions → decides when to use them → framework executes → result fed back to LLM

> 💡 **Analogy:** An LLM without tools is like a genius locked in a room with no phone, no computer, no books. Smart, but can't *do* anything in the real world.

---

## 7. Backstory — The Personality

Shapes *how* the agent approaches work:

| Backstory | Behavior |
|-----------|----------|
| "Seasoned researcher" | Digs deeper, finds hidden gems |
| "Meticulous planner" | Organized, detailed output |
| "Budget-conscious advisor" | Always finds the cheapest option |
| No backstory | Bland, generic responses |

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
- Example: Researcher → Planner → Booker

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
| Like... | A genius locked in a room who can only talk | A smart assistant with a phone, laptop, and car keys |
| You say "plan my vacation" | Gives you tips about how to plan | Actually searches flights, compares hotels, checks your budget, returns options |

### Real Example

**You:** "Find me cheap flights to Bali next month"

| ChatGPT | AI Agent |
|---------|----------|
| "Here are some tips for finding cheap flights: use Google Flights, be flexible with dates..." | *searches flight APIs* → *compares 47 options* → *checks your calendar* → "Here are 3 flights: Emirates $450 on June 12, Singapore Air $380 on June 15..." |

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
Agent tries to search for flights → API returns 500 error

❌ Without error handling: agent crashes or loops forever

✅ With error handling: 
   → retries once → still fails 
   → tells user "Flight API is down, here's what I found from cached data instead"
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
Agent is planning a vacation...
Finds a hotel for $400/night

❌ Without HITL: Books a $2000/night suite because it matched "luxury" in your goal. Oops.

✅ With HITL: "I found Hotel X at $400/night, 4.8 stars, private beach. Should I book it?"
   → User says yes → agent books it
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
Role: Travel agent
Goal: Help with vacation planning
```

```
✅ GOOD:
Role: Elite luxury travel consultant specializing in Southeast Asian destinations
Goal: Find exactly 3 beach resorts in Bali with private villas under $3000/week, 
      rated 4.5+ stars, with airport transfer included
Backstory: You've personally visited 200+ resorts. You reject anything touristy 
           or overcrowded. You prioritize hidden gems over popular chains.
Expected Output: Markdown table with: Resort name, price/night, rating, 
                 distance from airport, unique selling point
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
| Vacation planner | ECS/AgentCore | Multiple searches, might take minutes |

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
    result = agent.run("Find flights to Bali")
    assert "search" in [step.tool_name for step in result.steps]
    assert "price" in result.output.lower()
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
