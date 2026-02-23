# Google
## Search
Unfortunately, Google removed public access to their Search API while this project was in development. As a result, the `SearchWeb` endpoint no longer functions. Since it was mainly used intrinsically (though it could also be used extrinsically), it's a bit of a bummer but doesn't significantly hinder the Agent's core functionality. Alternatively, Google Search could be activated directly within the Agent, but this hasn't been implemented yet due to the late realization of the API's revocation.

![Google Search API removed](Pasted image 20260219221102.png)

## Gemini API
The free Google Gemini API used to be quite generous when we started this implementation, offering 15 RPM (Requests Per Minute) and 250 RPD (Requests Per Day). This was the original reason I chose this LLM. Sadly, these limits have since been reduced to 5 RPM and 20 RPD. This means you will hit the free API limit much faster now, to the point where it can severely hinder productivity and usability.

19.02.2026
![Gemini API rate limits](Pasted image 20260219221208.png)

| **Metric**                    | **Mid-2025 (Old Limits)** | **Current (Post-Dec 2025)** | **The Drop** |
| ----------------------------- | ------------------------- | --------------------------- | ------------ |
| **RPM** (Requests Per Minute) | 15                        | **5**                       | -66%         |
| **RPD** (Requests Per Day)    | 250                       | **20**                      | Up to -98%   |
| **TPM** (Tokens Per Minute)   | 1,000,000                 | **250,000**                 | -75%         |

# Ai Based
# 1. Hallucinations

Whenever you are working with LLMs Hallucinations can become a problem. 

The model may produce confident-sounding information that is **incorrect, unverified, or not grounded** in real system data (e.g., inventing ticket IDs, statuses, users, or past actions).

In this specific case the context windows should in most cases not become very long, making Hallucinations less likely. You will still have to keep in mind that it is still very much possible (especially when you have the ai make important changes). This happens more on cheaper models.

### Mitigations

- **Tool-first grounding:** For any claim about internal data, require a tool call (`searchTickets`, `getTicketInfo`, etc.).
- **No-guessing rules:** If uncertainty is high, ask clarifying questions (e.g., the “80% rule”).
- **Citations in UI:** Include ticket references (keys/links) in responses so users can verify.
- **Post-tool response policy:** Only confirm actions *after* tool execution succeeded.
- **Validation checks:** Verify `projectName`/`assignee` via canonical lists (prevents invented names).

---

## 2. Context Loss

The assistant forgets or misapplies prior conversation details. It can forget which ticket is being discussed, confuse projects/users and fails to respect previously stated constraints.  THis can lead to the wrong ticket being selected, repeated questions etc. This can aswell be counteracted by using more capable models

### Mitigations
- **Persisted conversation memory:** Store messages as `USER/AI/SYSTEM/TOOL` and reload per request.
- **Explicit anchoring:** Echo key identifiers in responses (“ticket JNG-123”, “project Jenga-Service”).
- **Context fallback rules:** Only use `ticketId=null` / `projectId=null` when the user explicitly says “this/current/here”.
- **Session-aware UI:** Make “current ticket” visible and stable in the frontend to reduce ambiguity.

---

## 3. Latency

Due to the inherently high latency of querries to an LLM, there is alot of latency the endpoint needs to account for. This can be partially counteracted with the use of streaming, but has not been implemented yet in this project due to the generally short awnsers weve been getting from the LLM. Tool calls etc. dont introduce a whole lot of latency in comparrison

### Mitigations (recommended)
- **Optimize prompt size:** Limit history,
- **Timeouts + graceful fallbacks:** Return partial results and ask user how to proceed.
- **Streaming responses:** Improves perceived latency even if total time is unchanged.
- **SLOs:** Define target p95 latency and alert when exceeded.


---

## 4. Privacy Concerns

Not all LLMs are DSGVO conform and therefore cannot and should not be used with sensitive information like userdata.

### Mitigations (recommended)
- **Access controls:** Restrict who can read conversation history and logs.

---

## 5. Rate Limiting

Some APIs have pretty strict ratelimiting which can hinder the functionality of the AI serverly. This an lead to failed request, lost data and potentially doubled tool calls -

### Mitigations (recommended)
- **Backoff + jitter:** Exponential backoff on 429/5xx, cap retries.
- **Circuit breakers:** If provider is throttling, fail fast with a helpful message.