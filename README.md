# Application 1: Rule Engine with AST

> **Zeotap | Software Engineer Intern | Assignment | Application 1**

## Applicant Introduction

Hi, I'm **AYUSH RAJ**, a dedicated and versatile digital media professional with a solid foundation in both **front-end and back-end development**. With experience working on **full-stack technologies**, I have knowledge of front-end frameworks like **HTML, CSS, and JavaScript**, along with proficiency in back-end technologies such as **Node.js, Python**, and databases like **SQL and NoSQL**. I’m also passionate about optimizing models, such as **RAG with OpenAI** and **Pinecone DB**, and continuously strive to stay updated on emerging trends in **web and app development**.

## Table of Contents
- [Introduction](#introduction)
- [Technical Parts](#technical-parts)
  - [Installation](#installation)
  - [How to Run](#how-to-run)
- [About Solution](#about-solution)
  - [Solution Overview](#solution-overview)
  - [Code Structure](#code-structure)
- [Non-Technical Parts](#non-technical-parts)
  - [My Approach](#my-approach)
  - [Feedback](#feedback)
- [Outro](#outro)

## Introduction

I read the assignment description several times to ensure I didn’t miss anything.  
Here are some key points about the code and the documentation:
- The code is heavily commented with docstrings (React code is compatible with Doxygen, and Python code with Sphinx).
- Unit tests cover both **positive** and **negative** cases, aiming for **80%+ coverage**.
- This README explains the **technical parts** first, followed by a discussion of the **solution** and concludes with **non-technical reflections**.

## Technical Parts

### Installation
The entire solution is containerized using **Docker** for platform-agnostic deployment. However, you can also run the components separately on your local machine.

#### Frontend
```bash
cd frontend
npm install
npm start
```

#### Backend
```bash
cd backend
pip install -r requirements.txt
python app.py
```

### How to Run

1. **Start the Backend**  
   Navigate to the backend folder and run:
   ```bash
   python app.py
   ```

2. **Start the Frontend**  
   From the frontend folder, run:
   ```bash
   npm start
   ```

## About Solution

### Solution Overview
The task involves building a **rule engine** that parses and processes logical expressions using **Abstract Syntax Trees (AST)**. The system can:
- Parse rules from strings.
- Evaluate conditions.
- Store and transform trees for further use.

### Code Structure
- **Backend**: Implements the AST-based rule evaluation logic.
- **Frontend**: Provides an interactive interface for creating and testing rules.

## Non-Technical Parts

### My Approach

1. **Understanding the Problem**:  
   I thoroughly read the assignment to grasp the requirements.

2. **Exploring Heuristics**:  
   I researched the **most frequent operator heuristic** and its use cases.

3. **Design Inspiration from Compilers**:  
   The assignment resembled a compiler project. My prior experience with **lex** and **bison** was helpful in designing the AST structure.

4. **Creating the Parser**:  
   I defined classes for nodes, operators, and conditions, then implemented a parser for rule evaluation.

5. **AST Traversal**:  
   I wrote traversal logic to:
   - Create AST from strings.
   - Evaluate conditions.
   - Serialize and store ASTs.

### Feedback
This assignment challenged me to apply my knowledge of **compilers**, **algorithms**, and **software engineering**. It gave me insights into **rule engine heuristics**, and I enjoyed working on it thoroughly!

## Outro

Thanks for reviewing my work! 😊
