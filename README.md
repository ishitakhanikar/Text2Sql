Text2SQL

A production-oriented Text-to-SQL system that converts natural language questions into safe, validated SQL queries, executes them against a database, and returns structured results. The project emphasizes correctness, safety, modularity, and clarity over raw prompt demos.

⸻

Table of Contents
	•	Overview
	•	Problem Statement
	•	System Architecture
	•	Project Structure
	•	Setup and Installation
	•	Python Version
	•	Virtual Environment
	•	Dependencies
	•	Environment Variables
	•	Core Pipeline
	•	Prompt Engineering
	•	Model Abstraction
	•	SQL Validation and Execution
	•	Error Handling
	•	Running the Application
	•	Design Decisions
	•	Limitations
	•	Future Improvements

⸻

Overview

Text2SQL enables users to query relational databases using plain English. Instead of manually writing SQL, users can ask questions such as:

“Show districts where Scheduled Caste households contributed more person-days than Non-SC households”

The system translates the request into SQL, validates it for safety, executes it, and returns results in a structured format.

This project is intentionally designed as a multi-stage pipeline rather than a single prompt call, making it suitable for real-world use cases.

⸻

Problem Statement

SQL is powerful but inaccessible to many domain experts. Common challenges include:
	•	Lack of SQL proficiency
	•	Complex schemas
	•	Risk of unsafe or destructive queries
	•	Difficulty debugging incorrect generated queries

Text2SQL addresses these issues by combining controlled prompt engineering, strict validation, and modular execution.

⸻

System Architecture

User Question (Natural Language)
        ↓
Prompt Construction Layer
        ↓
Language Model (Local or API)
        ↓
SQL Validation Layer
        ↓
Query Execution Engine
        ↓
Structured Results

Key architectural principles:
	•	Clear separation between generation and execution
	•	No direct execution of raw model output
	•	Modular components with single responsibilities

⸻

Project Structure

text2sql/
│
├── main.py                  # Application entry point
├── config.py                # Central configuration
│
├── prompts.py               # Prompt templates for SQL generation
├── utils.py                 # Shared helper utilities
│
├── model_server.py          # Model routing and orchestration
├── localmodel.py            # Local LLM loading and inference
├── OpenAI.py                # OpenAI API integration
├── Mistral.py               # Mistral API integration
│
├── subprocess_manager.py    # Isolated subprocess execution
├── query_execution.py       # SQL execution and result handling
│
├── requirements.txt
└── README.md

Each module is intentionally isolated to simplify debugging and future extension.

⸻

Setup and Installation

Python Version

Use Python 3.12 for maximum compatibility with modern ML, database, and async libraries.

python --version
# Python 3.12.x


⸻

Virtual Environment

Create and activate a virtual environment:

python3.12 -m venv venv
source venv/bin/activate

Using a virtual environment is mandatory to avoid dependency conflicts.

⸻

Dependencies

Install all required dependencies:

pip install -r requirements.txt

All dependencies are pinned to ensure reproducible environments across machines.

⸻

Environment Variables

Set the required environment variables depending on the selected model backend:

OPENAI_API_KEY=your_openai_key
MISTRAL_API_KEY=your_mistral_key
DATABASE_URL=your_database_connection_string

Environment variables should be placed in a .env file and must never be committed to version control.

⸻

Core Pipeline
	1.	User submits a natural language query
	2.	Database schema and context are loaded
	3.	A structured prompt is constructed
	4.	The language model generates SQL
	5.	SQL is validated for safety and structure
	6.	The validated query is executed
	7.	Results are returned to the user

Each stage is logged and independently testable.

⸻

Prompt Engineering

Prompt templates are defined in prompts.py and are designed to:
	•	Restrict generation to known schema elements
	•	Prevent hallucinated tables or columns
	•	Encourage deterministic SQL output
	•	Explicitly forbid destructive operations

This controlled prompting significantly improves reliability over free-form generation.

⸻

Model Abstraction

The system supports multiple model backends through a unified interface:
	•	Local language models
	•	OpenAI API models
	•	Mistral API models

The model_server.py module selects the backend at runtime based on configuration, allowing easy experimentation without code changes.

⸻

SQL Validation and Execution

Before execution, all generated SQL passes through a validation layer that:
	•	Blocks destructive queries (DROP, DELETE, ALTER)
	•	Enforces read-only access
	•	Performs basic syntactic checks

Validated queries are then executed in a controlled environment via query_execution.py.

⸻

Error Handling

The system provides structured error handling for:
	•	Invalid or ambiguous user queries
	•	Model generation failures
	•	SQL validation errors
	•	Database execution errors

Errors are returned with actionable messages instead of raw stack traces.

⸻

Running the Application

Start the application:

python main.py

The program will prompt for a natural language question and display both the generated SQL and its execution results.

⸻

Design Decisions

Key design choices include:
	•	Separating SQL generation from execution
	•	Using strict prompts instead of creative generation
	•	Abstracting model providers
	•	Prioritizing safety and predictability

These decisions favor reliability over novelty.

⸻

Limitations
	•	Accuracy depends on schema clarity
	•	Complex analytical queries may require refinement
	•	No automatic query optimization

These trade-offs are intentional to maintain safety.

⸻

Future Improvements

Potential extensions include:
	•	Query explanation generation
	•	Confidence scoring for SQL outputs
	•	Interactive clarification questions
	•	Web-based user interface
	•	Support for additional databases

⸻

Summary

Text2SQL demonstrates a disciplined approach to applying language models to structured data problems. By combining controlled prompting, model abstraction, and strict execution safeguards, the project moves beyond simple demos toward a reliable and extensible Text-to-SQL system.