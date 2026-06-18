# Voice Agents + ElevenLabs Platform Brief
**Date:** 2026-06-18 | **Research method:** /last30days + targeted WebSearch verification
**Context:** Exploring ElevenLabs as the platform for deploying AI voice agents to small businesses in India

---

## Part 1: Voice Agents Landscape (last30days research)

🌐 last30days v3.3.2 · synced 2026-06-18

**The 1-second cliff is real and dominating engineering decisions** - Per [AI Engineer](https://www.youtube.com/watch?v=N7b1PJc7SFc) (Rishabh Bhargava, Together AI, 5.4K views): users notice latency above 500ms and hang up above one second. Network latency from models in a different data center adds 30% overhead; co-locating everything drops that to ~5ms. Sub-600ms is the production floor.

**Platform choice has crystallized into three distinct jobs-to-be-done:**
- **Vapi** - orchestration layer for developers, full LLM/TTS/STT/telephony control ($0.05/min + API costs, 500-600ms latency)
- **Retell AI** - polished build-and-ship, fastest to production (580-620ms, $0.07-0.15/min, HIPAA-ready)
- **Bland AI** - outbound-call-at-scale, deterministic Pathways flows (~800ms, volume-optimized)

Per [devaland.com](https://devaland.com/blog/elevenlabs-vs-vapi-vs-retell-voice-ai-comparison): "they get pitted against each other constantly - but they don't actually solve the same problem."

**ElevenLabs is no longer just TTS - it launched a full platform play** - $500M raise at $11B valuation (Feb 2026). Conversational AI 2.0 launched March 6: natural turn-taking, integrated RAG, multimodal (text + voice), HIPAA compliance, ~50% price cut. Now a direct Vapi/Retell competitor.

**Conversation design outperforms model upgrades in conversion** - [@SwenRomijn](https://x.com/SwenRomijn/status/2067204314163024107) (91 likes): "I changed one line in my AI voice agent - the greeting - and conversion went up nearly 30%. No new voice, no new script logic."

**Interruption detection is still the hardest unsolved problem** - Barge-in detection (stopping mid-sentence when caller interrupts) is where most agents break. LiveKit Agents reports 86% precision / 100% recall with dynamic endpointing - currently the highest public number.

**Research sources:** 13 Reddit threads (4,486 upvotes, 1,216 comments) from r/AI_Agents, r/LocalLLaMA, r/artificial | 18 X posts (1,684 likes) | 1 YouTube video (5,486 views) | 9 web pages | 5 GitHub items

---

## Part 2: ElevenLabs Platform Deep-Dive

### 2.1 Deployment Channels

ElevenLabs lets you define an agent once and deploy it across multiple channels from one dashboard:

| Channel | Notes |
|---|---|
| **Web widget** | Embed script + custom HTML element. Handles all audio in browser. |
| **Phone (inbound + outbound)** | Via Twilio or SIP trunking |
| **WhatsApp Business** | Native dashboard integration |
| **Custom API / SDK** | TypeScript SDK for fully custom UI |
| **VPC** | AWS SageMaker, Google Cloud Vertex (available now) |
| **On-premise** | Own servers/data centers (early access H1 2026) |
| **On-device** | Edge hardware, offline (early access H1 2026) |
| **IBM partnership** | Enterprise agentic AI (March 2026) |

### 2.2 Custom LLM / Bring Your Own Provider

Fully supported as a first-class feature. Docs: [elevenlabs.io/docs/agents-platform/customization/llm/custom-llm](https://elevenlabs.io/docs/agents-platform/customization/llm/custom-llm)

- Point ElevenLabs at any **OpenAI-compatible API endpoint** - your URL, your credentials
- Supported: GPT-4, Claude, Gemini, Groq, Together AI, DeepSeek R1, Cloudflare Workers AI, Azure OpenAI
- User brings own API key; ElevenLabs bills for voice I/O, your provider bills for inference
- **Hard requirement**: model must support tool use / function calling
- **LLM cascading**: configure backup LLM for automatic failover

### 2.3 Web Widget - How It Works

The widget is a pre-built browser component. Embed is two lines:

```html
<script src="https://elevenlabs.io/convai-widget/index.js" async></script>
<elevenlabs-convai agent-id="your-agent-id"></elevenlabs-convai>
```

Works in: plain HTML, WordPress, Webflow, Wix, React/Next.js, static sites.

What it handles automatically: WebSocket audio streaming, mic capture, STT, LLM inference, TTS playback, turn-taking, interruption detection.

**Conversation modes** (configurable in dashboard):
- Voice only (default)
- Voice + text (user can switch mid-conversation)
- Chat mode (text-only, no voice)

**Customization**: colors, shapes, avatar image, button labels, language.

**Security**: agents must be "public" (no auth). Mitigate with domain allowlist (Security tab) - widget only initializes on approved domains.

For fully custom UI with ElevenLabs backend: use the TypeScript SDK.

### 2.4 Pricing

| Plan | Cost | Conversational AI |
|---|---|---|
| Free | $0 | ~15 min/month, no overage |
| Starter | $5/month | Limited, no overage |
| Creator | $22/month | Per-minute overage billing unlocked |

**Start on Free** to test widget + custom LLM integration. **Creator at $22/month** is the minimum for real usage (overage billing unlocked).

### 2.5 Programmatic Access: REST API, CLI, and MCP Server

ElevenLabs has full programmatic access across three methods - all verified against live docs and GitHub.

**REST API**

Full CRUD for agent management. Key endpoints:
- `POST /agents` - create agent
- `GET /agents` - list agents
- `GET /agents/{id}` - get config
- `PATCH /agents/{id}` - update settings

Also covers: knowledge base, phone numbers, conversations, widget config. Official SDKs in **Python** and **TypeScript**. No limit on number of agents per plan (limited only by concurrent calls and monthly credits).

Docs: [elevenlabs.io/docs/api-reference/agents/create](https://elevenlabs.io/docs/api-reference/agents/create)

**CLI (`@elevenlabs/agents-cli`)**

Git-style workflow for agent config management:

```bash
elevenlabs agents list          # list agents on your account
elevenlabs agents pull          # pull configs from platform to local files
elevenlabs agents push          # push local changes back to platform
```

Edit agent configs as local files, version control them in git, push changes. Useful for CI/CD. API key stored in `~/.agents/api_keys.json`.

Docs: [elevenlabs.io/docs/eleven-agents/operate/cli](https://elevenlabs.io/docs/eleven-agents/operate/cli)

**Official MCP Server**

Repo: [github.com/elevenlabs/elevenlabs-mcp](https://github.com/elevenlabs/elevenlabs-mcp)

Works with Claude Desktop, Claude Code, Cursor, Windsurf, and any MCP-compatible host. Configure once with your ElevenLabs API key - Claude can then interact with ElevenLabs directly via tool calls without touching the dashboard.

Official MCP capabilities: TTS generation, voice cloning, audio transcription, Conversational AI agent interactions, outbound call triggering.

For agent + knowledge base management specifically: [github.com/jezweb/elevenlabs-mcp-server](https://github.com/jezweb/elevenlabs-mcp-server) - a community MCP built explicitly for this.

**What Claude can do once MCP is configured:**
- Create agents with specified system prompt, voice, LLM endpoint
- Update agent config (change greeting, adjust prompt, swap LLM provider)
- Manage knowledge base documents
- Trigger outbound test calls
- Pull the widget embed code for a specific agent

The web embed stays a single two-line code block per site. MCP/API handles everything upstream: agent definition, config, prompt iteration, knowledge base - all from the conversation.

**MCP setup steps:**
1. Get ElevenLabs API key from dashboard
2. Install: `npx @elevenlabs/mcp` or clone github.com/elevenlabs/elevenlabs-mcp
3. Add to Claude Code MCP config with the API key
4. Test by asking Claude to create a test agent and verify it appears in the ElevenLabs dashboard

### 2.6 Commercial Licensing

**Scenario A - Agency/done-for-you model:**
- You build and manage agents for client businesses
- Standard commercial use on any paid plan (Starter+)
- You pay ElevenLabs, charge clients separately at any margin
- No special agreement needed

**Scenario B - SaaS platform where clients self-manage agents:**
- Requires [ElevenLabs OEM Terms](https://elevenlabs.io/oem-terms) - a separate enterprise agreement
- Standard plans do not cover sublicensing
- Restrictions: cannot further redistribute; must operate as a business entity; government deployments need ElevenLabs consent
- Practical advice: start with Scenario A, revisit B when there's proven demand for self-serve

---

## Part 3: India-Specific Constraints

### 3.1 Phone Integration for India

**The Twilio problem**: Twilio does not reliably offer local Indian (+91) phone numbers. Their own documentation acknowledges a "how to use Twilio when phone numbers are unavailable in your country" scenario. Using Twilio gives a US/UK number - creates trust friction with Indian users receiving calls from foreign numbers.

**The correct India path: SIP Trunking**

ElevenLabs supports SIP trunking as a first-class integration. Two India-specific providers are directly named in ElevenLabs' SIP integration documentation:

- **[Exotel](https://exotel.com)** - major Indian cloud telephony provider, widely used across Indian startups and enterprises, fully regulated for India voice traffic
- **[Ozonetel](https://ozonetel.com)** - described as "purpose-built for India's contact center landscape"

**Setup flow with SIP:**
1. Sign up with Exotel or Ozonetel
2. Provision a +91 number
3. Configure SIP trunk pointing to ElevenLabs
4. ElevenLabs agent now has a genuine Indian number for inbound and outbound
5. Indian callers see a local number - no trust friction

Pricing through Exotel/Ozonetel is also significantly cheaper per minute for India traffic than Twilio's rates.

**ElevenLabs SIP trunking docs:** [elevenlabs.io/docs/eleven-agents/phone-numbers/sip-trunking](https://elevenlabs.io/docs/eleven-agents/phone-numbers/sip-trunking)

### 3.2 WhatsApp Integration for India

**Critical policy change - January 15, 2026:**
Meta banned general-purpose AI chatbots from the WhatsApp Business API. Source: [TechCrunch](https://techcrunch.com/2025/10/18/whatssapp-changes-its-terms-to-bar-general-purpose-chatbots-from-its-platform/) and [respond.io clarification](https://respond.io/blog/whatsapp-general-purpose-chatbots-ban).

| Status | Definition |
|---|---|
| **Banned** | Open-domain AI, answers anything, functions as a general AI assistant |
| **Allowed** | Task-scoped: customer support, appointment booking, order tracking, lead qualification, FAQs, payment reminders |

The agent's role must be "ancillary to a legitimate business service." For small business deployments (booking, support, orders) this lines up exactly with what Meta permits.

**India-specific setup requirements for WhatsApp Business API:**

Documents needed for Meta Business Verification:
- Certificate of Incorporation
- GST Certificate
- PAN Card
- Udyog Aadhar

Timeline: Meta review takes **3-10 business days** (start this early). Every outbound message template requires pre-approval from Meta. A dedicated phone number is required (cannot use a personal WhatsApp number).

**BSP requirement**: In India, WhatsApp Business API is typically accessed through a BSP (Business Solution Provider). Common options: Gupshup, Interakt, MSG91, Wati.

ElevenLabs native WhatsApp integration connects to your WhatsApp Business Account in the ElevenLabs dashboard - but you must complete Meta's verification independently first.

**ElevenLabs WhatsApp docs:** [elevenlabs.io/docs/eleven-agents/whatsapp](https://elevenlabs.io/docs/eleven-agents/whatsapp)

---

## Part 4: Recommended Path for India Small Business Deployment

### Immediate (testing phase)
1. Sign up for ElevenLabs Free account
2. Build a test agent with a task-scoped prompt (e.g. appointment booking)
3. Test web widget embed with your own custom LLM endpoint
4. Validate the BYOP integration works as expected

### Parallel (long lead time items)
5. Start Meta Business Verification process now (3-10 day review)
6. Gather: Certificate of Incorporation, GST Certificate, PAN Card, Udyog Aadhar
7. Choose a BSP (Gupshup or Interakt recommended for India)

### Near-term (once Free tier validated)
8. Upgrade to Creator ($22/month) for overage billing
9. Sign up with Exotel or Ozonetel for a +91 phone number
10. Configure SIP trunk from Exotel/Ozonetel to ElevenLabs
11. Test inbound and outbound call flow

### Go-live considerations
12. **Business model**: Start with Scenario A (agency - you build for clients) to avoid OEM Terms complexity
13. **WhatsApp scope**: Explicitly define agent as task-scoped in system prompt - no open-domain responses
14. **Domain allowlist**: Add your client domains to ElevenLabs widget Security tab before deploying to clients

### MCP setup (do this alongside testing)
15. Get ElevenLabs API key from the dashboard
16. Install elevenlabs-mcp: `npx @elevenlabs/mcp` or clone [github.com/elevenlabs/elevenlabs-mcp](https://github.com/elevenlabs/elevenlabs-mcp)
17. Add to Claude Code MCP config with the API key
18. Test: ask Claude to create a test agent via MCP, verify it appears in the ElevenLabs dashboard
19. From that point Claude can create/update/manage agents and knowledge bases without you touching the dashboard

---

## Key Sources

- ElevenLabs Conversational AI 2.0 announcement: [elevenlabs.io/blog/conversational-ai-2-0](https://elevenlabs.io/blog/conversational-ai-2-0)
- Custom LLM docs: [elevenlabs.io/docs/agents-platform/customization/llm/custom-llm](https://elevenlabs.io/docs/agents-platform/customization/llm/custom-llm)
- SIP trunking: [elevenlabs.io/docs/eleven-agents/phone-numbers/sip-trunking](https://elevenlabs.io/docs/eleven-agents/phone-numbers/sip-trunking)
- WhatsApp docs: [elevenlabs.io/docs/eleven-agents/whatsapp](https://elevenlabs.io/docs/eleven-agents/whatsapp)
- OEM Terms: [elevenlabs.io/oem-terms](https://elevenlabs.io/oem-terms)
- WhatsApp chatbot ban: [TechCrunch](https://techcrunch.com/2025/10/18/whatssapp-changes-its-terms-to-bar-general-purpose-chatbots-from-its-platform/)
- Platform comparison: [aiagentrank.io/blog/vapi-vs-retell-vs-bland-2026](https://aiagentrank.io/blog/vapi-vs-retell-vs-bland-2026)
- REST API - create agent: [elevenlabs.io/docs/api-reference/agents/create](https://elevenlabs.io/docs/api-reference/agents/create)
- CLI docs: [elevenlabs.io/docs/eleven-agents/operate/cli](https://elevenlabs.io/docs/eleven-agents/operate/cli)
- Official MCP server: [github.com/elevenlabs/elevenlabs-mcp](https://github.com/elevenlabs/elevenlabs-mcp)
- MCP server (agents + KB management): [github.com/jezweb/elevenlabs-mcp-server](https://github.com/jezweb/elevenlabs-mcp-server)
- ElevenLabs developers overview: [elevenlabs.io/developers](https://elevenlabs.io/developers)
- Raw last30days engine output: ~/Documents/Last30Days/voice-agents-raw-v3.md
