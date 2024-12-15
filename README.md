# Application 1 : Rule Engine with AST

> Zeotap | Software Engineer Intern | Assignment | Application 1

## Applicant Introduction

Hi, I'm Subhodeep Manna, a Full-Stack Developer and AI enthusiast skilled in React, Next.js, Django, and machine learning. I'm currently working on projects like a College Directory App and a Seat Booking System, always eager to learn and innovate!
[LinkedIn](https://www.linkedin.com/in/subhodeep21/).

## Table of Contents

- [Introduction](#introduction)
- [Technical Parts](#technical-parts)
  - [Installation](#installation)
  - [How to run](#how-to-run)
- [About Solution](#about-solution)
  - [Solution Overview](#solution-overview)
  - [Code Structure](#code-structure)
- [Non Technical Parts](#non-technical-parts)
  - [My Approach](#my-approach)
  - [Feedback](#feedback)
- [Outro](#outro)

## Introduction

> I read the assginment description a lot of times, so as to make sure I don't
> miss on anything important.

Most of the code I have written would seem like a story, so I made sure the README
doesn't :D. Everything would mostly be in bullets hereafter:

- code is heavily commented and accompany suitable docstrings (React code
  compatible with Doxygen, and Python code with Sphinx).
- Unittests are written keeping in mind all edge cases that could occur, there are
  positive as well as negative test cases. Coverage too is kept above 80%.
- This README will talk about the technical parts first, then about the solution.
  And later about all miscellenous things, which are although technical, yet I put
  them in the "non-technical" bracket. They talk more about my thinking and approach
  towards the solution, and a brief discussion on the design is also present.

## Technical Parts

### Installation

I have tried my best to make it platform agnostic, and packaged everything into a
Docker Container. You can also execute the below seperately for running them on
your machine without Docker.

- Frontend
  ```bash
  cd frontend/
  npm install
  ```
- Backend

  If `poetry` isn't previously installed, install it first.

  ```bash
  python3 -m venv $VENV_PATH
  $VENV_PATH/bin/pip install -U pip setuptools
  $VENV_PATH/bin/pip install poetry
  ```

  Then continue to create a venv, and install dependencies

  ```bash
  cd backend/
  poetry install
  ```

- Database

  After a `poetry install`, execute the following to setup raw data in your
  postgres instance. Make sure to populate the connection creds in the `.env`.

  ```bash
  docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 -d postgres
  ```

### How to run

Expecting that above installation process, suceeded.

- Frontend
  ```bash
  npm run dev
  ```
- Backend
  ```bash
  poetry run python main.py --dev
  ```
- Database
  ```bash
  docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -p 5432:5432 -d postgres
  ```

## About Solution

The solution, as expected and listed down in the doc, contains a UI, an API, and a database.
For UI, I opted for Next.js (because I knew it the best). API is writen with FastAPI, because
it is really fast both post deployment, and while writing code. System Performance is my
foremost priority, only to be followed by developer productivity.

### Solution Overview

The solution is pretty simple, I'll jot down a few bullets about the solution -

- The UI just makes HTTP calls to the REST API and visualizes the AST using `react-d3-tree`.
- The backend parses the raw string by first tokenizing using `tokenizer()`. We have a specified
  regex to look for lexems during the tokenization process. Then it parses the stream of tokens
  using a `Parser` object. This outputs a AST node.
- We store this AST in database by serializing it to JSON, and storing the JSON. ALthough there
  could be efficient ways to store in the database, like hashing the Node object and storing it
  in a KV store, with hash : Node object storage. This way we can traverse the tree for changes, and
  save only the nodes which went through a change. We can have dynamic pointers associated with each Node
  so that we can optimize for space too. Like two trees having same condition, will be stored only once.
  But a pointer and an associated entry will be made in a table. This abstraction can save space while
  still maintaining object isolation (for data security). Something similar to how Dropbox stores files
  on its servers.

### Code Structure

This section will talk about files, and what they do.

- Backend
  file_path | description
  ----------------|-----------------------
  main.py | Entrypoint of the server
  poetry.lock | Dependencies for the server, managed by poetry
  pyproject.toml | Py Project Metadata
  rule_engine/ast_utils.py | Contains Class definitions and Utility functions around the AST like Node, etc
  rule_engine/database.py | ORM and Functions to read/write through database
  rule_engine/main.py | Entrypoint to the API, contains API contracts
  rule_engine/models.py | DB Table Schema
  rule_engine/parser_utils.py | Parser and Tokenizer definitions and utilities

- Frontend
  file_path | description
  ----------------|-----------------------
  app/ | Source Code of the App
  app/components/ASTTree.tsx | Component to visualize AST
  app/services/api.ts | Middleware/Utility to connect to REST API
  app/page.tsx | Entrypoint to the UI, renders the main pageV

### Design Discussion

Low-level design of classes:

```java
Class Node:
    Attribute node_type: String  // "operand" or "operator"
    Attribute left: Node
    Attribute right: Node
    Attribute value: Condition or Operator

Class Operator:
    Method evaluate(left: Node, right: Node) -> Boolean

Class ANDOperator inherits Operator:
    Method evaluate(left: Node, right: Node) -> Boolean:
        return left.value.evaluate() AND right.value.evaluate()

Class OROperator inherits Operator:
    Method evaluate(left: Node, right: Node) -> Boolean:
        return left.value.evaluate() OR right.value.evaluate()

Generic Type T
Class Condition<T>:
    Attribute lvariable: String
    Attribute rvalue: T
    Attribute comparison_type: String  // "gt", "lt", "eq", "lteq", "gteq", "neq"

    Method evaluate(input_value: T) -> Boolean:
        If comparison_type == "gt":
            return input_value > rvalue
        If comparison_type == "lt":
            return input_value < rvalue
        If comparison_type == "eq":
            return input_value == rvalue
        If comparison_type == "lteq":
            return input_value <= rvalue
        If comparison_type == "gteq":
            return input_value >= rvalue
        If comparison_type == "neq":
            return input_value != rvalue

Class AST:
    Attribute root: Node

    Method evaluate_rule(json_data: Dictionary) -> Boolean:
        return _evaluate_node(root, json_data)

    Method _evaluate_node(node: Node, data: Dictionary) -> Boolean:
        If node.node_type == "operand":
            return node.value.evaluate(data[node.value.lvariable])
        If node.node_type == "operator":
            return node.value.evaluate(node.left, node.right)

    Method create_rule(rule: String) -> Boolean:
        tokens = tokenize(rule)
        parser = Parser(tokens)
        root = parser.parse()
        return True

    Function combine_rules(rules: List<String>) -> Boolean:
      // Step 1: Parse each rule into its AST form
      Attribute asts: List<Node> = []
      For each rule in rules:
          tokens = tokenize(rule)
          parser = Parser(tokens)
          asts.append(parser.parse())

      // Step 2: Determine the most frequent operator
      Attribute operator_count: Dictionary<String, Integer> = {'AND': 0, 'OR': 0}
      For each rule in rules:
          operator_count['AND'] += count_occurrences(rule, 'AND')
          operator_count['OR'] += count_occurrences(rule, 'OR')

      Attribute most_frequent_operator: String
      If operator_count['AND'] >= operator_count['OR']:
          most_frequent_operator = 'AND'
      Else:
          most_frequent_operator = 'OR'

      Attribute operator_class: Class
      If most_frequent_operator == 'AND':
          operator_class = ANDOperator
      Else:
          operator_class = OROperator

      // Step 3: Combine all ASTs using the most frequent operator
      While length(asts) > 1:
          left_ast = asts.pop(0)
          right_ast = asts.pop(0)
          combined_ast = Node(
              node_type="operator",
              left=left_ast,
              right=right_ast,
              value=operator_class()
          )
          asts.append(combined_ast)

      root = asts[0]
      Return True

```

Parser and Tokenizer

```java
Function tokenize(rule: String) -> List<String]:
    token_pattern = compile_regex('\s*(=>|<=|>=|&&|\|\||[()=><!]|[\w]+)\s*')
    Return [token for token in find_all(token_pattern, rule) if token.strip()]

Class Parser:
    Attribute tokens: List<String>
    Attribute pos: Integer

    Constructor(tokens: List<String>):
        tokens = tokens
        pos = 0

    Method parse() -> Node:
        If tokens is empty:
            Raise ValueError("Empty tokens list")
        Return parse_expression()

    Method parse_expression() -> Node:
        node = parse_term()
        While pos < length(tokens) AND tokens[pos] in ('AND', 'OR'):
            operator = tokens[pos]
            pos += 1
            right = parse_term()
            If operator == 'AND':
                node = Node("operator", left=node, right=right, value=ANDOperator())
            Else If operator == 'OR':
                node = Node("operator", left=node, right=right, value=OROperator())
        Return node

    Method parse_term() -> Node:
        If tokens[pos] == '(':
            pos += 1
            node = parse_expression()
            pos += 1  // skip ')'
            Return node
        Else:
            Return parse_condition()

    Method parse_condition() -> Node:
        lvariable = tokens[pos]
        pos += 1
        comparison_type = tokens[pos]
        pos += 1
        rvalue = tokens[pos]
        pos += 1
        If is_digit(rvalue):
            rvalue = to_integer(rvalue)
        Else If is_float(rvalue):
            rvalue = to_float(rvalue)
        Else:
            rvalue = strip_quotes(rvalue)
        condition = Condition(lvariable, rvalue, comparison_type)
        Return Node("operand", value=condition)
```

## Non Technical Parts

The technical parts have been discussed in these docs, and the code also describes it
so I wanted to give some space to talk about the non-technical stuff, although it relates
to everything technical only, but it's more about how I approached the solution rather than
the solution itself.

### My Approach

My Approach was pretty simple.

- The first thing I did was read the assignment deeply to understand what exactly it
  was asking us to implement.
- My second step was to do some literature reading about most frequent operator heuristic,
  and understood what it meant.
- This work was very similar to how parsers work in compilers. Having worked with `lex` and `bison`
  before during my coursework, I could easily decipher how I should appraoch this problem.
- So I drew an AST, and udnerstood the components, each Node, a Logical Operator, all binary operators,
  and Conditions, etc. So, I created class definitions for all of them, and identified the relationship
  between them.
- My next task was very simple, create a parser for it, and devise an algorithm to traverse the tree.
- Traversing the tree would enable 3 things - creation from string, evaluating a condition, and storing
  the tree.
- After I wrote code for it, it became a pretty straight forward task.

### Feedback

So, honestly this was a good question. Had to use my knowledge of compilers, algorithmic knowledge
and software engineering principles. It hit all 3 important pillars, academics, DSA and Dev.
It felt great to do this assignment, I also got to learn a lot about the heuristics in this space,
and I am thankful for it.

## Outro

Thanks.
