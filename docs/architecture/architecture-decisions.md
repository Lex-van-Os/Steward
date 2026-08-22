# Steward architecture decisions

## About
This document aims at providing an overview of architectural decisions that have been made for the development of the Steward project. By defining architectural decisions, we can ensure that the project can maintain a focused scope. Maintaining a focused scope is beneficial for the contributors of this project, including coding agents such as Claude. These architectural decisions are defined in this file, which is discoverable by AI coding agents.

## Architectural decisions

### 1. API framework
FastAPI has been choosen as the API framework for the development of the steward-api project. This decision is based on the following considerations:
1. Auto-generated OpenAPI docs - FastAPI provides automatically generated OpenAPI docs, which is beneficial for quick consumation of this API by the steward-web and steward-ai projects.
2. Developer wishes - The developer of this project wishes to improve his Python knowledge, in the domains of AI and API development. The developer has past experience with the FastAPI framework, and has noticed that there is a high demand for Python tech stacks that include experience with FastAPI.
3. Natural SQLAlchemy and Alembic matching - SQLAlchemy and Alembic have been choosen as technologies that will be used for this project. FastAPI seems to be the API framework with the best support for these technologies, when compared with other competitors.
4. Maturity and popularity - FastAPI has proven to be a widely adopted framework, on par with other alternatives such as Django and Flask.
5. Async model - FastAPI has built-in support for ASGI, which is beneficial for a project such as Steward, where communication goes from a web server to a back-end AI project and requests may contain long-lived connections.

### 2. Storage
For storage in this AI focused Python project, the decision has been made to go with PostgreSQL with the pgvector extension. This is based on the following considerations:
1. Simplicity - PostgreSQL offers a solution for both relational data storage, and vector data. This greatly favors simplicity and the KISS principle, over having to set-up and manage multiple databases for relational data and vector data. This simplicity also applies for deployment, since it needs only one container to run and back-up. 
2. Maturity - PostgreSQL has proven to be a mature option for both relational and vector data, increasing the guarantee of stable AI project output, and decreasing the likeliness of having to rewrite / refactor parts of the code.
3. Fit - Although dedicated vector database solutions might be beneficial for performance in the long term, the decision to go with PostgreSQL also fits the scenario of a smaller scale pet project without production scale adaptation. This includes considerations regarding vector fit, operational overhead, and scaling.

### 3. System architecture
System architecture has been categorized into multiple sub-chapters, that together form the argumentation for the design of the choosen architecture.

#### Deployment
Docker running on Docker Compose has been choosen as the technology to facilitate the deployment of the Steward project.

*Reason:*
Use of Docker containers with Docker Compose has been choosen, because of a match between simplicity of use, and the flexibility and scaleability regarding the deployment of the application.

*Argumentation:*
Why Docker Compose has been choosen over competitors:
1. Setup - Docker Compose offers the simplest set-up, which greatly fits the context of a pet project.
2. Service fit - The Steward project will probably run 4-5 services, which also fits the decision to choose Docker Compose over a larger framework such as k3s.
3. Maintenance - Docker Compose offers low maintenance, with the trade-off of less scaleability. Less scaleability is of less importance with the scale of a pet project.
3. Scaling - Docker Compose offers little to no scaling, which is beneficial for this project, since it needs none, and thus removes the need of extra maintenance to guarantee scaling. The use of Docker offers the possibility to migrate to a different composing platform over Docker Compose, in case the use-case of the project changes.

#### Reverse proxy
Caddy has been choosen to serve as a reverse proxy solution for the Steward project.

*Reason:*
The reason for choosing Caddy is mainly the simplicity it offers over other competitors, which leaves more time and focus for the development process.

*Argumentation:*
Why Caddy has been choosen over competitors:
1. Automation and maintenance - Caddy offers automatic HTTPS and certificate support, and offers low maintenance, which is the aim with this project, considering the focus op development, instead of infrastructure maintenance.
2. Docker Compose fit - Although a solution such as Traefik might be a better fit with automatic Docker Compose maintenance, Caddy offers a better fit considering the simplicity of running Docker Compose services and the simplicity of one config file mounted as a volume.
3. Performance - Although Caddy performs less compared to competitors, this isn't a big issue considering the scale of the project. 

### 4. Embedding model
bge-small-en-v1.5, by BAAI, has been choosen as the solution for an embedding model. This is based on the following argumentation:
1. No schema impact - bge-small-en-v1.5 outputs 384-dimensional embeddings, matching the dimensionality of the originally considered all-MiniLM-L6-v2, so it requires no changes to the pgvector column definition or index chosen in decision 2.
2. Retrieval quality - bge-small-en-v1.5 uses a newer training pipeline than older small embedding models such as all-MiniLM-L6-v2, and consistently outperforms them on retrieval benchmarks at a comparable model size, while still running comfortably on CPU without requiring a GPU.
3. License - bge-small-en-v1.5 is released under the MIT license.
4. Local development and scale - Flavor descriptors and tasting notes are short text, so a small, general-purpose embedding model running locally on CPU is sufficient. This also keeps flavor and tasting note data on the self-hosted server, instead of sending it to a third-party embedding API, matching the self-hosted approach chosen for deployment in decision 3.

### 5. Communication pattern
Synchronous REST/JSON over HTTP has been choosen as the communication pattern between the steward-api and steward-ai projects. This is based on the following argumentation:
1. Consistent tooling - Both services are built with FastAPI (see decision 1), so REST/JSON keeps a single contract and tooling approach (OpenAPI) across the system, instead of introducing a second schema system such as Protobuf for internal calls.
2. Workload fit - The embedding model chosen in decision 4 generates embeddings for short text in well under a second on CPU, making calls between steward-api and steward-ai quick and predictable. This is the scenario synchronous calls are suited for, rather than the slow or unpredictable workloads a message queue is intended to solve.
3. Scale - Steward is a single-user, self-hosted application with low query volume. Both gRPC's multiplexing advantage and a message queue's backpressure and decoupling advantages target call frequencies and traffic patterns well beyond what Steward will produce.
4. Simplicity and debugging - REST/JSON calls can be inspected directly with tools such as curl or a browser, and require no additional infrastructure (message broker, worker processes) or tooling (Protobuf code generation) to run and maintain.
