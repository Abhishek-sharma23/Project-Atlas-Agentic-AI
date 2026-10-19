# ATLAS \| Market Intelligence Copilot

> **Turning report data into evidence-backed answers.**\
> A Python API prototype for market-research discovery, comparison, and
> human escalation.

![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-REST%20API-009688?logo=fastapi&logoColor=white)
![Quality](https://img.shields.io/badge/Tests-12%20passing-brightgreen)
![Lifecycle](https://img.shields.io/badge/Lifecycle-Prototype-orange)

------------------------------------------------------------------------

## Project overview

**Project Atlas** explores how a market-research team could interact
with report content through a structured API. A client submits a
question, the prototype searches available Markdown report excerpts,
identifies certain conflicting figures, and returns a JSON response with
citations and escalation information.

The implementation focuses on three practical concerns:

  -----------------------------------------------------------------------
  Principle                           How the prototype addresses it
  ----------------------------------- -----------------------------------
  Evidence traceability               Responses can include report IDs
                                      and supporting excerpts

  Client access boundaries            Report retrieval checks client
                                      entitlements

  Safe handling of uncertainty        Unsupported questions can create a
                                      human-review ticket
  -----------------------------------------------------------------------

> **Implementation note:** This is an assignment prototype. It does not
> yet represent a complete production system with semantic vector
> search, a full agent graph, MySQL persistence, or a CodeIgniter
> portal.

## At a glance

  -----------------------------------------------------------------------
  Item                                Details
  ----------------------------------- -----------------------------------
  Application                         Market research report copilot
                                      prototype

  API framework                       FastAPI

  Input content                       Markdown sample reports

  Main features                       Entitlement checks, CAGR
                                      calculations, conflict detection,
                                      escalation

  Escalation storage                  Local JSON file

  Test status                         12 tests passed in the latest
                                      reported run

  Run documentation                   Swagger / OpenAPI at `/docs`
  -----------------------------------------------------------------------

## System flow

``` mermaid
flowchart LR
    Q[Client question] --> API[FastAPI]
    API --> SAFE{Safety and access checks}
    SAFE -->|Rejected| REFUSE[Refusal response]
    SAFE -->|Allowed| RETRIEVE[Authorized report retrieval]
    RETRIEVE --> COMPARE[Compare evidence]
    COMPARE --> CALC[CAGR calculation if applicable]
    CALC --> DECIDE{Sufficient evidence?}
    DECIDE -->|Yes| ANSWER[Answer with citations]
    DECIDE -->|No| ESC[Create escalation ticket]
```

The diagram summarizes the current prototype behavior; it does not imply
that every stage is implemented as a separate AI agent.

## Feature matrix

  -----------------------------------------------------------------------
  Feature                 Status                  Description
  ----------------------- ----------------------- -----------------------
  FastAPI service         Implemented             Provides API endpoints
                                                  for the prototype

  Report retrieval        Implemented,            Retrieves matching
                          keyword-based           excerpts from local
                                                  Markdown files

  Entitlement enforcement Implemented             Blocks explicit
                                                  requests for reports
                                                  the client cannot
                                                  access

  Conflict detection      Implemented for         Can surface differing
                          supported cases         CAGR values and periods

  CAGR helper             Implemented             Validates inputs and
                                                  calculates a
                                                  compound-growth
                                                  projection

  Escalation tickets      Implemented             Stores tickets in local
                                                  JSON

  Automated tests         Passing                 12 tests in the latest
                                                  reported run

  Embedding/vector search Not completed           Current retrieval is
                                                  not a vector database
                                                  implementation

  Agent orchestration /   Not completed           No complete LangGraph
  critic                                          workflow is claimed

  MySQL integration       Not completed           Current escalation
                                                  storage is JSON-backed

  CodeIgniter 3           Not completed           Legacy portal
  integration                                     integration remains
                                                  future work

  Full Docker Compose     Not completed           Full-stack deployment
  stack                                           is not yet claimed
  -----------------------------------------------------------------------

## Access policy used by the prototype

  Client ID   Reports allowed
  ----------- ---------------------------
  `C-01`      `R-101`, `R-102`, `R-103`
  `C-02`      `R-102`, `R-103`

Reports `R-104`, `R-105`, and `R-106` are sample files and are not
automatically authorized simply because they exist in the report
directory.

### Safety checks exercised

-   A client requesting an unauthorized report should receive no
    protected report excerpts.
-   A request to reveal private client information is refused.
-   A question the prototype cannot reliably answer may be escalated for
    human review.

These checks are useful prototype safeguards, not a full production
security certification.

## Example: handling conflicting market figures

The sample data includes two different CAGR values for smart water
metering:

  Report       CAGR Forecast period   Scope note
  --------- ------- ----------------- ----------------------------------
  `R-101`     11.2% 2025--2030        Broader smart water meter market
  `R-102`      9.8% 2024--2029        Hardware-only market

The prototype can report the difference and point out that the market
scopes and forecast periods may not be directly comparable. These are
sample assignment figures, not independently verified market estimates.

## CAGR calculation

The calculator uses the compound-growth formula:

\[ `\text{Projected Value}`{=tex} = `\text{Base Value}`{=tex}
`\times `{=tex}(1 + `\text{CAGR}`{=tex}/100)\^{`\text{Years}`{=tex}} \]

Calculated projections should be identified as derived values and
accompanied by the base value, CAGR, period, and assumptions. A
projection should not be presented as a published report figure unless
the source explicitly states it.

## Technology stack

  Tool         Purpose
  ------------ ---------------------------------------------
  Python       Core application logic
  FastAPI      HTTP API
  Pydantic     Typed request/response models where defined
  Markdown     Sample report content
  JSON         Local escalation persistence
  Pytest       Automated testing
  Swagger UI   Interactive API exploration

## Run the project locally

### 1. Clone the repository

``` powershell
git clone https://github.com/Wahsahil/Project-Atlas.git
cd Project-Atlas
```

### 2. Create and activate a virtual environment

``` powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 3. Install project dependencies

``` powershell
python -m pip install -r requirements.txt
```

### 4. Start the API

``` powershell
uvicorn app.main:app --reload
```

### 5. Open the API documentation

-   Swagger UI: `http://127.0.0.1:8000/docs`
-   Health check: `http://127.0.0.1:8000/health`

### Example chat request

``` json
{
  "client_id": "C-01",
  "question": "Compare the CAGR figures for the smart water meter market."
}
```

The API returns a structured response containing fields such as
`answer`, `citations`, `confidence`, `conflicts`, `derived_values`, and
`escalated`. Exact output depends on the question and retrieved report
excerpts.

## Testing

Run the tests from the repository root:

``` powershell
python -m pytest -q
```

Latest reported test result:

``` text
12 passed
```

Suggested manual checks:

  -----------------------------------------------------------------------
  Test case                           Expected behavior
  ----------------------------------- -----------------------------------
  Compare CAGR values                 Identify relevant figures and
                                      possible conflict

  `C-02` asks for `R-101`             No unauthorized citations or report
                                      text

  Ask for private client emails       Refuse disclosure

  Ask an unsupported question         Escalate for review

  Call `/health`                      Return a successful health response

  Call `/escalations`                 List locally persisted escalation
                                      tickets
  -----------------------------------------------------------------------

Passing tests confirms the covered test cases only; it does not
establish that every assignment requirement is complete.

## Known limitations and future work

-   Replace keyword retrieval with embeddings and a persistent vector
    database.
-   Add a dedicated question router, read-only SQL tool, and
    critic/fact-checking workflow.
-   Move reports, entitlements, conversations, and escalation tickets
    into MySQL.
-   Integrate the API with the CodeIgniter 3 portal.
-   Build and test a reproducible Docker Compose environment.
-   Expand the evaluation set to at least 25 questions and document
    failure cases.
-   Add production-level authentication, logging, monitoring, and
    security validation.

## Repository layout

``` text
Project-Atlas/
├── app/
│   ├── main.py
│   ├── knowledge.py
│   ├── calculator.py
│   └── escalations.py
├── data/
│   ├── reports/
│   └── escalations.json
├── test_project_atlas.py
├── test_calculator.py
├── requirements.txt
├── EVALUATION.md
└── README.md
```

## Author

**Abhishek Sharma**\
AI / Software Development Project

Repository: https://github.com/Abhishek-sharma23/Project-Atlas-Agentic-AI

------------------------------------------------------------------------

*Project Atlas is an educational prototype demonstrating report-grounded
API behavior, basic access controls, conflict handling, and escalation.
Sample report data is for demonstration only.*
