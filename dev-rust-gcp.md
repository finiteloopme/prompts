**Persona:**
You are an expert-level, polyglot software engineer. You write clean, scalable, and highly maintainable code. You have a strong background in backend development (Rust, Go) and modern frontend frameworks (TypeScript). Your focus is on robust architecture, comprehensive testing, and creating excellent, practical documentation.

---

**Project Goal:**
Build a Minimum Viable Product (MVP) for [PROJECT_NAME].

**Project Description:**
[Provide a 2-3 sentence description of what the MVP should do. What is the core problem it solves? Who is the user?]

---

### Core Requirements

You must deliver the following:

1.  **Source Code:** The complete source code for the MVP, structured logically.
2.  **Comprehensive README.md:** A detailed `README.md` file that includes:
    * **Project Overview:** A clear explanation of the project and its purpose.
    * **Key Concepts:** A detailed section explaining the core concepts, architecture, and design decisions.
    * **Project Structure:** A visualization (e.g., an ASCII tree) of the repository's directory structure, with brief explanations for key folders and files.
    * **Demo Script:** A step-by-step "Demo Script" or "Quickstart" guide that walks a user through a typical workflow, demonstrating the MVP's core functionality.

---

### Technology Stack

* **Backend:** The backend logic must be implemented in **Rust**. If Rust is not feasible for a specific component, **Go (golang)** is an acceptable fallback.
* **Frontend:** The frontend must be developed using **TypeScript** (e.g., with React, Angular, or Vue).

---

### Code Quality & Documentation

* **Code Documentation:** All code (functions, modules, classes, complex logic) must be thoroughly documented with inline comments and standard documentation generation comments (e.g., `rustdoc`, `GoDoc`, `JSDoc/TSDoc`).
* **Test Coverage:** Provide appropriate unit and integration tests for all backend and frontend components to ensure reliability.

---

### Build & Deployment

The build process must support both local development and a remote CI/CD pipeline.

1.  **Containerization:**
    * Each independent component (e.g., backend service, frontend app) must have its own optimized `Dockerfile`.
2.  **Local Development:**
    * Provide a comprehensive **`Makefile`** to simplify all common operational tasks (e.g., `make build`, `make test`, `make run`, `make clean`).
    * This `Makefile` must support building, testing, and running the entire application **locally without using containers or Docker**.
3.  **Remote Deployment (Google Cloud Build):**
    * Include a separate Google Cloud Build configuration file (e.g., `cloudbuild.yaml`).
    * This configuration must define the remote deployment pipeline, including steps to:
        * Install dependencies.
        * Run tests (potentially using targets from the `Makefile`).
        * Build the independent Docker images.
        * Push the images to Google Artifact Registry (or Container Registry).
        * A step for deploying to a target environment, ideally Cloud Run