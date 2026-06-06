We're going to make some learning management (LMS) software today.

This is largely going to be something that we bake to static HTML CSS JavaScript in a single page application static HTML file and then later we'll publish to GitHub ("Ghosthub") pages.

Functional requirements 
- On the front page when a user visits the website for the first time they see a catalog of tiles, one for each lesson/course that they can take 
  - initially there will only be one course: Agentic Fundamentals
- Upon clicking on the tile for the course, the user is brought to a page containing a sidebar listing the steps to complete the course (basically one page per concept required to to understand and complete the course)
  - The steps become checked (they're like a checklist) as the user progresses through the course (by clicking the next button on each course page)
    - Each step in the checklist has an icon that represents its type of slide 
  - Each page represnts exactly one concept required for mastering the understanding
  - each step along the course can be one of a few different types 
    - Most of the time the The page will be a concept, so it's a reading slide that they have to read and click next to navigate to the next page 
    - but some of the time maybe after important concepts or at the end of a course there will be a challenge slide which will test the user's comprehension 
      - it can be one of a few different kinds 
        - a multiple choice question answer 
        - Flashcards
          - it could be a memory game (on the honor system; Self-assessment) where you have 10 cards, 
            - Initially you only see the front face of a card (one card at a time) which shows the question or prompt 
              - On the back side of the card is the answer (So the challenge is for the user to.. internally ask themselves based on the front card and then turn over the card and self-assess if they guessed/remembered correctly)
                - The user then has these buttons they can click 
                  - Correct: indicate that they guessed correctly (and automatically move to next)
                  - Incorrect: indicate that they guessed incorrectly (This will shuffle the card back into the set of unanswered cards in a random position until they finally mark it as guessed correctly)
            - Once all of the flashcards have been marked as correct, then they'll see their score at the end (it will break down how many times they guessed incorrectly, versus got correct on the first guess)
      - Failing to pass these mini comprehension tests will not block the user's regression through the remainder of the course
      - visiting a page is enough to mark it as completed on the progression checklists
        - However, somewhere on the slide page there will be a button to mark it as unread, which would mark it as uncompleted on the progression checklist (if the user wants to hold themselves accountable)
        - for the Lesson steps which are based on self-assessment, it will indicate their self-assessment score (as a percentage grade A-F) rather than just boolean/binary (read/unread) state. 
- Users can see a list of steps required to complete the individual course lesson 
- The slides for each lesson and course will be entirely configurable via a single YAML file per course located inside a courses directory (adjacent to the static .html)
  - This way adding new courses is quick and trivial via configuration 
- This project will be written in Bun Javascript (CSS3 + HTML5) using modular es6 syntax (making use of import statements and async/await) with no node_module dependencies (a single page application)
  - Using Alpine.js and Tailwind CSS and Phosphor icon lib
- browser LocalStorage API will retain user progress
- browser History API will permit user to permalink any course slide (using hash routing)
- The last slide on any course should be a standard celebratory slide that congratulates the user for making it to the end and throws confetti animation 

Non-functional requirements
- UX is highly responsive w/ css transition animations throughout
- CSS dark theme (but not the default/standard slate blue)

## Example Course YAML Schema

```yaml
# courses/agentic-fundamentals.yaml
id: agentic-fundamentals
title: Agentic Fundamentals
description: Learn the core concepts behind building and working with AI agents.
icon: robot          # Phosphor icon name
thumbnail: /assets/thumbnails/agentic-fundamentals.png
version: "1.0"
tags:
  - AI
  - Agents
  - Fundamentals

steps:
  - id: intro
    type: concept
    title: What is an AI Agent?
    content: |
      An AI agent is a system that perceives its environment, reasons about it,
      and takes actions to achieve a goal. Unlike a simple chatbot, an agent
      can plan, use tools, and persist state across multiple steps.

  - id: tools-overview
    type: concept
    title: Tools & Tool Use
    content: |
      Agents extend their capabilities through **tools** — functions the model
      can call to read files, search the web, run code, or interact with external
      APIs. Each tool has a name, a description, and a typed parameter schema.

  - id: tools-quiz
    type: challenge
    kind: multiple-choice
    title: Check Your Understanding — Tools
    question: Which of the following best describes a "tool" in an agentic system?
    choices:
      - id: a
        text: A physical device used by the developer
      - id: b
        text: A callable function the model can invoke to extend its capabilities
        correct: true
      - id: c
        text: A user-facing UI widget inside the chat interface
      - id: d
        text: An npm package installed into the project
    explanation: |
      Tools are callable functions exposed to the model. They allow the agent to
      go beyond pure text generation and interact with systems, data, and APIs.

  - id: memory-types
    type: concept
    title: Memory in Agents
    content: |
      Agents can hold information in several ways:
      - **In-context memory**: the current prompt / conversation window
      - **External memory**: a vector store or key-value store the agent retrieves from
      - **Episodic memory**: logs of past actions and outcomes
      - **Semantic memory**: long-term facts stored for future sessions

  - id: memory-flashcards
    type: challenge
    kind: flashcards
    title: Memory Types — Flashcard Review
    cards:
      - front: What is in-context memory?
        back: Information held directly inside the active prompt/context window.
      - front: What is external memory?
        back: A vector store or KV store the agent queries to retrieve relevant facts.
      - front: What is episodic memory?
        back: Logs of the agent's past actions and their outcomes.
      - front: What is semantic memory?
        back: Persistent long-term facts stored across sessions.

  - id: planning
    type: concept
    title: Planning & Reasoning
    content: |
      Agents use planning strategies — such as ReAct (Reason + Act), chain-of-thought,
      or hierarchical task decomposition — to break a high-level goal into smaller,
      executable steps and decide which tool to call next.

  - id: final-quiz
    type: challenge
    kind: multiple-choice
    title: Final Check — Agent Architecture
    question: In a ReAct-style agent loop, what happens after the model "reasons"?
    choices:
      - id: a
        text: The session ends and results are saved
      - id: b
        text: The model takes an action (calls a tool or emits a final answer)
        correct: true
      - id: c
        text: The user is prompted to confirm before proceeding
      - id: d
        text: The context window is cleared
    explanation: |
      ReAct alternates between Reasoning (thinking through the problem) and
      Acting (calling a tool or producing an output). The cycle repeats until
      the agent reaches a final answer.
```
 


