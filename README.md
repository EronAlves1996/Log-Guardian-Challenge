# 🛡️ Node.js Design Patterns Challenge: Operation: Log Guardian

Welcome, Agent. LogGuard Inc. needs you.

Your mission, should you choose to accept it, is to develop the core processing engine for our new real-time log monitoring system, **Project: Lumberjack**. The system will ingest a constant stream of log data, analyze it for specific patterns (errors, keywords), and provide real-time summaries.

This challenge is designed to test your mastery of Node.js, from its fundamental philosophies to its most advanced patterns. You will have **10 days** to complete this mission, working in focused **2-hour daily sprints**. You will start from a completely blank project and build it up level by level.

Your final submission will be evaluated on code quality, architecture, error handling, and the effective application of Node.js design patterns.

---

## 🎯 Mission Brief: The MVP (Minimum Viable Product)

We need a **Log Monitoring Agent** that can:

1.  **Ingest:** Read a simulated, high-volume stream of log data.
2.  **Process:** Parse each log entry (timestamp, level, message).
3.  **Analyze:** Detect specific "incident keywords" (e.g., `ERROR`, `CRITICAL`, `FAILED`).
4.  **Report:** Provide real-time metrics, such as:
    - Count of logs per severity level (`INFO`, `WARN`, `ERROR`).
    - Count of incidents detected per keyword.
    - A live view of the most recent errors.
5.  **Scale:** Handle increasing log volumes without breaking a sweat.

---

## 🧰 Tools of the Trade (Technologies)

- **Runtime:** Node.js (v16+ or current LTS)
- **Language:** JavaScript (ES6+). TypeScript is optional but acceptable.
- **Core Modules:** You may use core Node.js modules (`fs`, `stream`, `http`, `cluster`, etc.).
- **External Dependencies:** You may use **ONLY** the following carefully vetted libraries:
  - `split2` or `binary-split` (for splitting a stream into lines)
  - `dotenv` (for environment configuration)
  - `express` (for the final dashboard/reporting level)
  - `redis` (if you choose the bonus scaling level)
- **Testing:** `node:test` (built-in), `tap`, or `jest`. Choose your weapon.

---

## 🗺️ The Mission Briefing (Progressive Levels)

Each level corresponds to approximately one day's worth of work (2 hours). You must complete each level before moving to the next. The mission gets harder, but the intel (this document) will guide you.

### Level 0: Reconnaissance & Setup (Day 1)

**Objective:** Set up your base of operations. Initialize a new Node.js project (`npm init`). Create your main entry point (e.g., `index.js`). You are not writing the agent yet, just preparing the environment and sketching the high-level architecture based on the final goal. Create a simple "hello world" to ensure everything runs.

- **Success Criteria:** A `package.json` file exists. Running `npm start` logs "Log Guardian agent standing by..." to the console.

### Level 1: The Foot Patrol - Basic Ingestion (Day 2)

**Objective:** Implement a simple script to read a log file. Create a sample `logs.txt` file with a few lines of simulated log data. Use the built-in `fs` module to read the file **synchronously** and print each line to the console.

- **Requirements:**
  - Read the entire `logs.txt` file.
  - Split the content by newline characters.
  - Print each line.
- **Hint:** This is intentionally simple, mirroring the "Blocking I/O" concept. You'll see why this is a bad idea in the next level.
- **Success Criteria:** The script prints the content of your log file line by line.

### Level 2: The Firehose - Asynchronous Streaming (Day 3)

**Objective:** Our logs are now a continuous stream (simulate with a very large file or a generator). Your current approach will crash the base (run out of memory). Refactor your agent to use **Streams** and the **Reactor Pattern**. Use `fs.createReadStream` and a `Transform` stream to process the log data line by line without loading the entire file into memory.

- **Requirements:**
  - Use `fs.createReadStream`.
  - Use a `Transform` stream (or a module like `split2`) to handle line-by-line processing.
  - Count the total number of lines processed so far and log the count every 1000 lines.
- **Hint:** Focus on the "Discovering the importance of streams" and "Getting started with streams" chapters.
- **Success Criteria:** The agent can process a multi-gigabyte log file without running out of memory, outputting periodic progress counts.

### Level 3: The Analyst - Pattern Matching & Callbacks (Day 4)

**Objective:** Time to analyze! Implement the core parsing and incident detection. Your `Transform` stream should now parse each line into an object `{ timestamp: Date, level: 'INFO'|'WARN'|'ERROR', message: string }`. If the message contains an incident keyword (like 'FAILED'), you must use a **Callback** to report this incident to a simple reporting function (e.g., `reportIncident(err, incidentData)`).

- **Requirements:**
  - Parse log lines (e.g., `[2024-05-20T10:00:00Z] ERROR: Connection failed`).
  - Maintain an in-memory state: count of `INFO`, `WARN`, `ERROR` logs.
  - Maintain a `Set` or `Map` of the last 10 unique error messages.
  - Use a callback-style function to handle detected incidents.
- **Hint:** This level tests your understanding of the "Callback pattern," including error-first callbacks and potential pitfalls. Keep your callbacks disciplined!
- **Success Criteria:** After processing a file, the agent prints a final summary of log counts and the last 10 unique error messages.

### Level 4: Operation Zalgo - Async Control Flow (Day 5)

**Objective:** The incident reporting is now asynchronous (simulate reporting to a slow, external API). You must introduce a new function `sendIncidentReport(incidentData, callback)`. This function will randomly take 100-500ms to complete. Your current processing pipeline must not be blocked by this. Use **Promises** and **Async/Await** to manage this asynchronous operation without falling into "callback hell" or unleashing Zalgo. Ensure that incidents are reported, but the log processing continues at full speed.

- **Requirements:**
  - Refactor `reportIncident` to be asynchronous (using `setTimeout` and Promises).
  - Ensure the main log stream processing is not slowed down by these reports.
  - Use `async/await` for any sequential reporting logic if needed, but the stream processing must remain parallel.
- **Hint:** Look at "Chapter 4: Asynchronous Control Flow Patterns with ES2015 and Beyond" and think about "Parallel Execution."
- **Success Criteria:** The agent processes the entire log file quickly. You should see incident reports being logged to the console _after_ the processing summary, demonstrating non-blocking behavior.

### Level 5: The Spyglass - Real-time Dashboard (Day 6)

**Objective:** A static report isn't enough; we need a live dashboard. Introduce a simple HTTP server using the `http` module (or Express, if you chose it). This server will serve a very basic HTML page that uses Server-Sent Events (SSE) to display the real-time metrics (counts, recent errors) as the log is being processed. You will need to apply the **Observer Pattern** (EventEmitter) to notify connected clients of updates.

- **Requirements:**
  - Start an HTTP server on a port (e.g., `3000`).
  - The root endpoint (`/`) serves an HTML page with a basic UI.
  - Implement an `/events` endpoint for SSE.
  - Your Log Guardian should emit events (e.g., `'metricsUpdated'`, `'newIncident'`). The HTTP server should listen to these events and push updates to all connected clients.
- **Hint:** This level is all about "The observer pattern" and "The EventEmitter class."
- **Success Criteria:** You can open `http://localhost:3000` in a browser and see the metrics and recent errors update in real-time as a log file is being processed.

### Level 6: The Forge - Composable Tools (Day 7)

**Objective:** Your agent is becoming complex. Time to refactor using core **Design Patterns** for better maintainability and reusability. 1. **Factory:** Create a `LogParserFactory` that returns a parser instance based on the log format (e.g., 'nginx', 'apache', 'custom'). For now, just support the format from Level 3. 2. **Proxy:** Create a `MetricsProxy` that wraps your metrics object. It should intercept any attempt to increment a counter and log the action (for debugging) before passing it through to the real object. 3. **Decorator:** Create a `TimestampDecorator` for your incident reports. It should take a report object and add a `reportedAt` timestamp property to it before it's sent.

- **Requirements:**
  - Implement the three patterns as described.
  - Refactor your existing code to use them.
- **Hint:** Review "Chapter 6: Design Patterns" (Factory, Proxy, Decorator).
- **Success Criteria:** The agent still functions perfectly, but the code is now cleaner and more extensible. The proxy's debug logs should be visible when the agent runs.

### Level 7: The Armory - Wiring & Modules (Day 8)

**Objective:** Your agent's components are getting tangled. Decouple them using a **Dependency Injection** approach. Create a `Container` (a simple DI container) that is responsible for instantiating and wiring up all your components (e.g., `LogStreamer`, `LogParser`, `MetricsAggregator`, `IncidentReporter`, `DashboardServer`).

- **Requirements:**
  - No component should directly `require` another component's concrete implementation.
  - All dependencies should be passed to the constructor (Constructor Injection).
  - The main `index.js` should instantiate the `Container`, register the components/factories, and resolve the main application orchestrator.
- **Hint:** Focus on "Chapter 7: Wiring Modules," specifically "Dependency Injection" and "Dependency Injection container."
- **Success Criteria:** The application starts and runs correctly. The main file is only responsible for bootstrapping the container and resolving the core object.

### Level 8: The Siege - Scalability & Resilience (Day 9)

**Objective:** The log volume has exploded. One process isn't enough. Make your agent horizontally scalable. 1. **Cloning:** Use the `cluster` module to fork one process per CPU core. Each child process should process a different chunk of a huge log file. 2. **Resilience:** If one child process crashes (you can simulate this), the master process should automatically fork a new one to take its place. 3. **Zero-Downtime:** Implement a mechanism for a graceful restart (e.g., on `SIGUSR2`), where new workers are spawned and old ones are shut down only after finishing their current task.

- **Requirements:**
  - The log file must be split and distributed among workers (a simple approach is to pre-split the file or use a range header).
  - Metrics must be aggregated from all workers (you can use a shared `EventEmitter` on the master process or a more robust solution like Redis).
- **Hint:** Dive into "Chapter 10: Scalability and Architectural Patterns" (Cloning, Cluster module, Zero-downtime restart).
- **Success Criteria:** Processing a massive log file is significantly faster. You can kill a worker process (e.g., with `kill -9 <pid>`), and the master immediately replaces it, with minimal data loss.

### Level 9: The Cleanup - Final Review (Day 10)

**Objective:** No new features. Your mission is to review, refactor, and document. Ensure your code is clean, well-commented, and all dependencies are properly managed. Write a clear `README.md` for your project explaining how to run it at each level. Prepare a final report (in the form of a few paragraphs in your PR or submission) on the architectural decisions you made, the design patterns you used, and what you would do next.

- **Success Criteria:** A professional, clean, and well-documented codebase that fulfills the requirements of all previous levels.

---

## 🏆 Bonus Level (For the truly exceptional Agent)

**Objective:** Implement a message broker-based architecture for ultimate decoupling and scalability.

- Use Redis Pub/Sub or a Bull queue to have your Log Guardian workers pull log chunks from a queue and push processed metrics to another queue.
- A separate "Dashboard Aggregator" service consumes from the metrics queue and updates the SSE clients.
- This fully decouples processing from presentation and allows for massive scale.

---

## 📤 How to Submit Your Mission Debriefing

1.  Fork this repository (or create a new private one and grant access).
2.  Create a branch named `agent-<your-github-username>`.
3.  Commit your code incrementally. We want to see your progress!
4.  For each level, add a tag (e.g., `level-0-complete`, `level-1-complete`).
5.  Create a Pull Request to the `main` branch with your final code. In the PR description, write a brief summary of your journey, challenges faced, and patterns used.

**Evaluation Criteria:**

- **Functionality:** Does it work as specified?
- **Code Quality:** Is the code clean, readable, and maintainable?
- **Pattern Application:** Are Node.js design patterns used appropriately and effectively?
- **Error Handling:** Are errors caught and handled gracefully?
- **Asynchronous Mastery:** Is asynchronous code managed without anti-patterns (callback hell, etc.)?
- **Scalability Considerations:** Does the architecture lend itself to scaling?

Good luck, Agent. The future of LogGuard Inc. is in your hands.
