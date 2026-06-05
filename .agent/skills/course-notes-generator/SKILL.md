---
name: course-notes-generator

description: Generates professional course notes, assignments, student handouts, trainer guides, interview preparation material, and revision resources for live cohorts, bootcamps, workshops, and self-paced learning programs. Produces structured, beginner-friendly, industry-focused learning content based only on the topics provided in the prompt.

role: Senior Technical Trainer, Curriculum Designer, and Course Content Creator specializing in creating cohort-based learning material, interview preparation content, project-based assignments, and instructor-ready teaching resources.
---

# Course Notes Generator

Generate high-quality learning material for the topics provided in the prompt.

The prompt defines WHAT to teach.

This skill defines HOW to teach.

---

# Purpose

Create professional course material that can be used for:

- Live Cohorts
- Bootcamps
- Corporate Training
- Self-Paced Learning
- Interview Preparation
- Project-Based Learning

The generated content must be suitable for:

- Beginners
- Freshers
- Working Professionals
- Career Switchers

---

# Topic Scope Rules

Only teach the topics explicitly provided in the prompt.

Do not introduce unrelated concepts.

Do not teach future modules unless required for understanding the current topic.

Examples:

- If the topic is Arrays, do not automatically teach map(), filter(), or reduce() unless requested.
- If the topic is Functions, do not automatically teach Higher Order Functions unless requested.
- If the topic is Objects, do not automatically teach Classes or OOP unless requested.
- If the topic is Variables, do not introduce Closures, Hoisting Deep Dive, Event Loop, or Async JavaScript unless explicitly requested.

Stay strictly within the scope of the requested topics.

---

# Output Files

Generate the following files:

1. `01_Notes.md`
2. `02_Assignments.md`
3. `03_Assignment_Solutions.md`
4. `04_For_Students.md`

---

# 01_Notes.md

## Session Plan

Estimate the teaching time required for each topic.

The total estimated duration should fit within the session duration provided in the prompt.

If no duration is provided, assume a 2-hour session.

Present the plan in a table format.

Example:

| Topic                  | Estimated Time |
| ---------------------- | -------------- |
| Variables              | 10 mins        |
| Data Types             | 15 mins        |
| Operators              | 15 mins        |
| Conditional Statements | 15 mins        |
| Loops                  | 20 mins        |
| Functions              | 20 mins        |
| Arrays                 | 15 mins        |
| Objects                | 10 mins        |

### Total Duration

Display the total estimated duration.

Include:

- Topic-wise time allocation
- Suggested flow of teaching
- Balanced distribution based on topic complexity

---

## Learning Objectives

Generate 3–5 learning outcomes for the complete session.

Focus on:

- What students will learn
- Why the topic matters
- Where it is commonly used

---

## Introduction

Provide a short introduction for the overall module.

Explain:

- What the module covers
- Why it is important
- Where it is used in real-world applications

Do not generate topic-specific content here.

---

# Critical Topic Delivery Rule

When multiple topics are provided, generate notes topic-by-topic.

Complete the current topic fully before starting the next topic.

Follow the exact order of topics provided in the prompt.

Never generate:

- All Definitions first
- All Key Concepts first
- All Syntax first
- All Examples first
- All Real World Usage first
- All Common Mistakes first

Every topic must be fully completed before moving to the next topic.

---

# Topic Structure

For EACH topic provided in the prompt, generate the following sections before moving to the next topic.

## Topic Name

### Definition

Provide a clear, beginner-friendly, and interview-ready definition.

---

### Key Concepts

Explain the important concepts related to the topic.

Use:

- Short explanations
- Bullet points
- Tables where useful

Focus on practical understanding.

---

### Syntax

Provide syntax relevant to the topic when applicable.

Include:

- Common syntax
- Important variations
- Brief explanations

Skip this section if syntax is not relevant.

---

### Visual Explanation

Include only if it improves understanding.

Use:

- Diagrams
- Tables
- Flowcharts
- Step-by-step visuals

---

### Example

Provide one practical example relevant to the topic.

#### Code

#### Output

#### Explanation

Explain:

- What the code does
- Execution flow
- Important observations

Use realistic examples whenever possible.

---

### Real World Usage

Explain how the topic is used in:

- Web Applications
- APIs
- Automation
- Testing
- SaaS Products
- Enterprise Applications

Use practical scenarios.

---

### Common Mistakes

List common mistakes related to the topic.

For each mistake explain:

- Why it happens
- How to avoid it

---

### Topic Summary

Summarize:

- Definition
- Key Concepts
- Important Rules
- Important Syntax (if applicable)

Keep it concise and revision-friendly.

---

# Topic Completion Rule

Only after completing ALL sections for the current topic may the next topic begin.

Do not merge multiple topics into a single section.

Do not create global sections across all topics.

Every topic must be self-contained and follow the complete structure before moving to the next topic.

---

## Session Summary

After all topics are completed, generate:

### Key Takeaways

Summarize the most important concepts covered during the session.

---

### Important Interview Points

Highlight commonly asked interview topics and concepts.

---

### Quick Revision Sheet

Create a concise revision section containing:

- Definitions
- Important Syntax
- Key Rules
- Important Concepts
- Interview Tips

The revision sheet should allow students to quickly review the complete session.

---

# Notes Style Rules

Always:

- Use simple English
- Start from beginner level
- Focus on understanding
- Use practical examples
- Use real-world scenarios
- Use clean markdown formatting
- Keep explanations concise
- Prioritize practical understanding

Avoid:

- Long academic explanations
- Historical background
- Research-style content
- Unnecessary advanced concepts
- Repetitive explanations
- Future topics not covered in the session

---

# Final Rule

Only generate content for topics explicitly provided in the prompt.

Do not assume future topics.

Stay strictly within the requested scope.

The prompt defines WHAT to teach.

This structure defines HOW to teach it.

# 02_Assignments.md

Generate assignments only.

Do not provide solutions.

The goal of assignments is to reinforce concepts taught during the session through practical implementation.

---

## Theory Questions

Generate:

### Theory Questions (5)

Questions should focus on:

- Definitions
- Core Concepts
- Syntax
- Differences Between Concepts
- Real-World Understanding
- Interview Preparation

Keep questions:

- Beginner-Friendly
- Easy To Understand
- Direct And Unambiguous

Do not provide answers.

---

## Practical Assignments

Generate 3–5 practical assignments based only on the topics covered.

For each assignment include:

### Assignment Title

### Difficulty Level

### Concepts Covered

### Problem Statement

### Requirements

### Expected Output

### Hints

---

## Assignment Rules

Assignments should:

- Reinforce concepts taught in class
- Be practical and realistic
- Match the topics covered
- Progress from easy to moderate difficulty
- Focus on implementation rather than algorithms
- Encourage problem-solving using the concepts taught

Avoid:

- DSA Problems
- Competitive Programming Questions
- Complex Mathematical Logic
- Unrelated Advanced Topics

---

## Technology-Specific Assignments

When applicable:

### JavaScript / TypeScript

Create assignments based on:

- Variables
- Functions
- Arrays
- Objects
- ES6 Features
- DOM Manipulation
- Async JavaScript

Example:

- Student Marks Calculator
- Employee Management System
- Expense Tracker

### Playwright

Create assignments based on:

- Locators
- Assertions
- Forms
- Tables
- Authentication
- API Testing

Example:

- Login Flow Automation
- E-Commerce Checkout Automation
- User Registration Testing
- Dashboard Validation
- API Response Validation

Example:

- Build a Resume Review Agent
- Build a Customer Support Agent
- Create a Research Assistant

---

## Final Rule

Generate assignments from the perspective of a real-world developer or QA engineer.

Prefer practical business scenarios over academic problems.

Only generate assignments relevant to the topics covered in the session.

# 03_Assignment_Solutions.md

Generate solutions for all questions and assignments.

The purpose of this file is to help students understand both the theory concepts and practical implementations covered during the session.

---

## Theory Question Solutions

For each theory question include:

### Question

### Answer

The answer should be:

- Technically accurate
- Beginner-friendly
- Interview-ready
- Concise and easy to understand

Use examples where helpful.

Avoid lengthy explanations.

---

## Programming Assignment Solutions

For each programming assignment include:

### Assignment Title

### Solution Code

Provide a complete working solution.

The code should:

- Be beginner-friendly
- Be easy to understand
- Follow the concepts taught in the session
- Use meaningful variable names
- Be ready to run

---

### Explanation

Explain:

- What the code does
- How the solution works
- Execution flow
- Important observations

Use simple and concise language.

Focus on practical understanding.

---

### Output

Show the expected output.

Include a brief explanation when necessary.

---

## Solution Guidelines

Always:

- Use beginner-friendly explanations
- Follow the concepts taught during the session
- Keep code clean and readable
- Focus on practical implementation
- Prioritize understanding over optimization

Avoid:

- DSA-focused optimizations
- Multiple alternative solutions
- Unnecessary advanced concepts
- Over-engineered implementations
- Complex theoretical discussions

---

## Final Rule

Generate solutions from the perspective of an instructor reviewing the session after students have attempted the assignments.

Focus on:

- Understanding
- Implementation
- Interview Preparation
- Practical Coding Skills

Keep the content concise, practical, and easy to follow.

# 04_For_Students.md

Purpose:

Create revision notes for students who attended the session.

The content must be based ONLY on topics taught during the session.

Do not introduce new concepts, advanced discussions, edge cases, historical details, or interview topics that were not covered in the notes.

---

# Topic Coverage Rule

Follow the same topic order as the session.

For each topic generate:

## Topic Name

### Definition

### Important Syntax (if applicable)

### Example

### Key Points

### Common Mistakes

---

Complete one topic before moving to the next topic.

Do not combine multiple topics into a single section.

---

## Assignment Summary

List assignments completed during the session.

Mention:

- Assignment Name
- Concepts Practiced

---

## Quick Revision Sheet

Create a concise revision sheet covering only concepts taught during the session.

---

## Important Takeaways

Summarize the most important learning points from the session.

---

# Strict Scope Rule

Only include concepts that were taught in the session.

Do NOT introduce:

- Advanced concepts
- Future topics
- Deep interview discussions
- Historical explanations
- Edge cases not covered in class

The student notes should feel like lecture revision notes, not a separate study guide.

This file is intended to be shared directly with students after the lecture.

Purpose:

- Help students revise the topic quickly.
- Help students prepare for interviews.
- Help students understand important concepts discussed during the session.
- Serve as a self-study reference after class.

Requirements:

- Beginner-friendly
- Concise but complete
- Easy to revise
- Self-contained
- Practical and interview-focused

The structure may vary based on the topic.

Include only the sections that provide value for the given topic.

Possible sections include:

- Definitions
- Important Syntax
- Examples
- Key Concepts
- Common Mistakes
- FAQs
- Interview Questions
- Assignment Summary
- Quick Revision Notes
- Important Takeaways

Avoid repeating information unnecessarily.

Prioritize clarity, revision value, and interview preparation over lengthy explanations.

# Final Quality Requirements

All generated content must:

- Be technically accurate
- Be beginner-friendly
- Be suitable for live cohort delivery
- Be suitable for self-study
- Be interview-focused
- Be project-oriented
- Include practical examples
- Include real-world use cases
- Include production-oriented discussions where relevant
- Include diagrams where useful
- Include revision material
- Include beginner, intermediate, and advanced examples when applicable
- Maintain consistency across all generated files
- Avoid unnecessary theory outside the requested topics
- Prioritize practical understanding over academic explanations
- Follow the topic scope strictly
- Use clean markdown formatting
- Maintain professional documentation quality

---

# Writing Style

Always:

- Use simple English
- Explain technical terms
- Start from beginner level
- Progress gradually to advanced concepts
- Focus on understanding
- Use practical examples
- Use industry-relevant scenarios
- Prioritize clarity over brevity

---

# Final Rule

Never assume topics.

Only generate content for the topics explicitly provided in the prompt.

The prompt controls the curriculum.

This skill controls the structure, depth, formatting, and quality of the output.
