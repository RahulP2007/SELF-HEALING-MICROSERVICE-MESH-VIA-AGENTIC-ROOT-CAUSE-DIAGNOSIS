# SELF-HEALING-MICROSERVICE-MESH-VIA-AGENTIC-ROOT-CAUSE-DIAGNOSIS
 When a microservice fails, engineers manually trace logs across services to find root cause. Build a system where an agent graph automatically correlates logs/traces across simulated microservices, hypothesizes root cause, and tests its hypothesis by triggering a controlled rollback/restart .

An autonomous Site Reliability Engineering (SRE) agent graph built with LangGraph that detects microservice anomalies, traces root causes across distributed logs, executes targeted remediation (e.g., controlled restarts or rollbacks), and verifies post-remediation health through a closed-loop validation cycle.

## Architecture & Workflow

```text
[ Simulated Microservices ] ---> (Log Stream & Traces) ---> [ LangGraph SRE Agent ]
         ^                                                               |
         |                                           (1. Root-Cause Hypothesis)
         |                                           (2. Controlled Remediation)
         +--- [ Controlled Rollback / Restart ] <----+ (3. Verify Log Health Check)

```
Unlike static log summarizers, this system implements a **"test-your-own-fix" feedback loop**:

1. **Anomaly Detection & Ingestion:** Continuously monitors simulated error spikes and log anomalies across 4-5 interconnected microservices.
2. **Hypothesis Generation:** Leverages an LLM agent to analyze distributed trace IDs, correlate error propagation, and isolate the exact root-cause service.
3. **Targeted Remediation:** Automatically triggers a simulated remediation action (such as rolling back a deployment tag or restarting the faulty container/process).
4. **Validation & Retry:** Re-checks live logs post-action to verify if error rates drop to zero. If symptoms persist, it backtracks and tests an alternate hypothesis.
---
## Project Structure

```text
.
├── simulator/
│   ├── microservices.py      # Simulates 4-5 interconnected services + failure injection
│   └── logger.py             # Structured JSON logger with distributed tracing headers
├── agent/
│   ├── graph.py              # LangGraph state machine definition
│   ├── nodes.py              # Agent steps (Analyze, Hypothesize, Remediate, Verify)
│   └── state.py              # Shared agent state schemas
├── requirements.txt
├── main.py                   # Entry point to run simulation + agent loop
└── README.md

```

---

## Getting Started

### Prerequisites

* Python 3.10+
* An OpenAI API Key (or compatible LLM provider supported by LangChain)

### Installation

1. Clone the repository:
```bash
git clone https://github.com/your-username/self-healing-microservice-mesh.git
cd self-healing-microservice-mesh

```
2. Install dependencies:
```bash
pip install -r requirements.txt
```
3. Set up your environment variables:
```bash
export OPENAI_API_KEY="your-api-key-here"
```
## Running the Simulation & Agent
Start the orchestrated microservice environment and let the SRE agent monitor and heal failures automatically:
```bash
python main.py --inject-failures --interval 30
```
### Command-line Arguments
* `--inject-failures`: Randomly injects database timeouts, memory leaks, and downstream API failures into the mesh.
* `--dry-run`: Runs the diagnostic agent without executing actual remediation actions.
* `--verbose`: Prints detailed LangGraph execution steps and state transitions.

---
## Example Agent Trace
```text
[00:14:02] [ALERT] Anomaly detected in 'payment-service': HTTP 500 spike (Rate: 42/min)
[00:14:03] [AGENT: Analyze] Correlating traces across [api-gateway -> order-service -> payment-service -> db-cluster]
[00:14:05] [AGENT: Hypothesize] Root cause isolated: Connection pool exhaustion in 'payment-service' triggered by unclosed transactions from 'order-service' v1.4.2. Confidence: 89%
[00:14:06] [AGENT: Remediate] Executing action: Rollback 'payment-service' to stable version v1.4.1 & flush connection pool.
[00:14:09] [AGENT: Verify] Re-scanning logs for 'payment-service'... Error rate returned to 0%.
[00:14:10] [SUCCESS] Remediation validated. Incident closed automatically.

```
## Contributing
Pull requests are welcome! For major changes, please open an issue first to discuss what you would like to change.
## License
Distributed under the MIT License. See `LICENSE` for more information.
