# Assignment: Generic Task Manager

## Introduction

As a developer at a productivity software company, you've been tasked with creating a flexible task management system that can handle different types of tasks - from simple to-do items to complex project tasks with additional metadata. The system needs to be built using TypeScript's generic capabilities to ensure type safety while maintaining flexibility.

### Starter Files

Start with a new project. You will need to:
- Initialize a new TypeScript project
- Create HTML, CSS, and TypeScript files
- Configure `tsconfig.json` for your development environment

## Requirements

Create a task management application that demonstrates the power of TypeScript generics:

1. Core Features:
   - Create a generic `Task<T>` class that can handle different task types
   - Implement a `TaskList<T>` class to manage collections of tasks
   - Support at least two different task types:
     - Basic tasks (title, description, completed status)
     - Enhanced tasks (basic properties plus due date and priority)

2. Implementation Requirements:
   - Use TypeScript generics for type safety
   - Implement proper interface definitions
   - Create type constraints where appropriate
   - Ensure proper error handling
   - Store tasks in localStorage

3. User Interface:
   - Add new tasks with appropriate fields
   - Display list of tasks
   - Toggle task completion status
   - Remove tasks from list
   - Support different views for different task types

4. Code Organization:
   - Separate concerns (task logic vs. UI logic)
   - Use proper TypeScript features (interfaces, types, generics)
   - Implement error handling
   - Add appropriate comments

## Deliverables

Submit the following:
- All TypeScript source files
- HTML and CSS files
- Brief documentation of your generic type implementation choices

## Conclusion

In this assignment, you've built a practical task management application that leverages TypeScript's powerful generic types. By implementing both basic and enhanced task types through a single generic class structure, you've experienced firsthand how generics enable flexible, type-safe code reuse. This project has combined your understanding of TypeScript fundamentals with real-world application development, preparing you for building more complex type-safe applications in your future work.