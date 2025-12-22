ToDo App

1. Live Preview
[DEMO LINK](https://petrykgit.github.io/test_vue-todo-app/)

2. Design Reference
This project implements a clean, modern, and highly functional ToDo list interface, focusing on exceptional user experience.

3. Technologies Used
- Vue 3	- A progressive JavaScript framework used for building a reactive and performant user interface.
- Vite	- Used for lightning-fast development tooling and highly optimized production builds.
- SCSS	- Provides powerful features like variables and nesting for organized and maintainable stylesheets.
- Axios - A promise-based HTTP client used for efficient communication with the backend API.

4. Getting Started
Follow these steps to set up and run the ToDo application locally.

Prerequisites
- Node.js
- npm

Installation
Clone the repository:
- git clone https://github.com/petrykgit/test_vue-todo-app/
- cd test_vue-todo-app

Install dependencies:
- npm install

Running the Project.
Start the development server:
- npm run dev

*The app will be available at `http://localhost:5173/`*

Build for Production:
- npm run build

*The optimized files will be generated in the `dist/` directory.*

5. Features.
This application showcases robust full CRUD functionality and advanced UI/UX practices for handling asynchronous operations:

- Full CRUD Functionality: Seamlessly Create, Read, Update (toggle completion, edit title), and Delete tasks.
- API Interaction Management: Efficiently handles asynchronous operations, preventing race conditions and ensuring data integrity.
- Visual Feedback: Incorporates loaders (spinners) and notifications to provide clear user feedback during ongoing API calls (adding, updating, deleting).
- Task Filtering: Users can filter the list to view all tasks, active tasks, or completed tasks.
- Toggle All: Single action to mark all tasks as completed or active.
- Clear Completed: Dedicated action to efficiently delete all completed tasks.
- Clean Architecture: Built with an emphasis on clean code, custom hooks (useTodos, useErrorNotification, useTodosFiltering), and strong TypeScript contracts for enhanced maintainability.