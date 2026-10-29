# 🎮 Fortnite Store - Full Stack Technical Challenge

![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring](https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white)
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white)
![Flyway](https://img.shields.io/badge/Flyway-CC0200?style=for-the-badge&logo=flyway&logoColor=white)

![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/css3-%231572B6.svg?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)
![Bootstrap](https://img.shields.io/badge/bootstrap-%238511FA.svg?style=for-the-badge&logo=bootstrap&logoColor=white)
![SweetAlert2](https://img.shields.io/badge/SweetAlert2-%23fe5f5f.svg?style=for-the-badge&logo=sweetalert2&logoColor=white)

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)

Full Stack Web Application. The system simulates an online store for Fortnite cosmetics, consuming real data from an external API and managing purchases, credits and users.

---

## 🛠️ Technologies Used

### Backend (API)
* **Java 21 (LTS):** Modern, high-performance base language.
* **Spring Boot 3:** Main framework for dependency injection and web.
* **Spring Security + JWT:** Secure stateless Authentication and Authorization.
* **Spring Data JPA (Hibernate):** Data persistence and ORM.
* **Flyway:** Safe database versioning and migration.
* **Oracle Database 21c XE:** Relational database (running in a container).

### Frontend (Client)
* **HTML5 & CSS3:** Semantic structure and custom styling.
* **JavaScript (Vanilla ES6+):** Client logic, API consumption (Fetch) and DOM manipulation without heavy frameworks.
* **Bootstrap 5:** CSS framework for fast responsiveness and UI components.
* **SweetAlert2:** Library for elegant, responsive alerts, modals and pop-ups (replacing the native `alert()`).

### Infrastructure & DevOps
* **Docker & Docker Compose:** Service orchestration (App + Database) ensuring the same environment in dev and prod.
* **Azure Virtual Machine (Linux Ubuntu):** Cloud production server.
* **Nginx:** High-performance web server acting as a reverse proxy for the Frontend.

---

## 💡 Relevant Technical Decisions

During development, some architectural decisions were made to ensure robustness and meet the requirements:

1.  **"Race Condition" Strategy (Oracle vs API):**
    * The Oracle database in a container takes a while to start. To avoid failures during API startup, an asynchronous `delay` mechanism (`@Async`) was implemented in the initial synchronization service, along with `depends_on` (healthchecks) in Docker Compose, ensuring the application only tries to access the database when it is 100% ready.

2.  **Data Synchronization (Cron):**
    * Spring's `@Scheduled` was used to run an automatic task every **1 hour**. This keeps the local storefront in sync with the official Fortnite API (which updates the shop daily), without overloading the external server on every user request.

3.  **Hybrid Deployment Architecture:**
    * To work around *Mixed Content* (HTTPS vs HTTP) and CORS restrictions in test environments without an SSL domain, I chose to host both the Frontend (via Nginx) and the Backend on the same Azure VM. This simplified networking, eliminated connection latency and ensured the system worked in an integrated way.

4.  **Flyway for Migrations:**
    * To guarantee the integrity of the database schema (especially when dealing with Oracle), I disabled Hibernate's `ddl-auto` and used **Flyway**. This ensures the tables (`TB_USUARIO`, `TB_COSMETICO`) are created deterministically and safely in any environment.

---

## 💻 How to Run Locally

The project is 100% dockerized to make it easy to run on any machine.

### Prerequisites
* [Docker](https://www.docker.com/) and Docker Compose installed.
* Git.

### Step by Step

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/MBDomingues/FORTINITE_PROJECT.git](https://github.com/MBDomingues/FORTINITE_PROJECT.git)
    cd FORTINITE_PROJECT
    ```

2.  **Start the application:**
    This command will download the images (Oracle and Java), build the project and start the containers.
    *The first run may take a few minutes due to the Oracle download.*
    ```bash
    docker-compose up --build
    ```

3.  **Wait for Startup:**
    * Follow the logs. Oracle will take some time to become "Healthy".
    * After the API starts, wait about **30 seconds** for the initial item synchronization.
    * Look in the log for: `SUCESSO: Sincronização base e status concluída` ("SUCCESS: Base and status synchronization completed").
    * If the initial synchronization fails due to hardware issues, clean up the container and restart the run.

4.  **Access:**
    * **Frontend:** Open the `fortnite-frontend/index.html` folder (using VS Code's Live Server is recommended) or adjust the URL in the JavaScript files to `localhost:8080`.
    * **API (Swagger/JSON):** [http://localhost:8080/api/v1/cosmeticos](http://localhost:8080/api/v1/cosmeticos)

---

## 🔌 Main Endpoints

| Method | Route | Description | Auth |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/v1/auth/cadastro` | Creates a new user (gets 10k V-Bucks) | Public |
| `POST` | `/api/v1/auth/login` | Authenticates and returns a JWT Token | Public |
| `GET` | `/api/v1/cosmeticos` | Lists cosmetics (filters and pagination) | Public |
| `GET` | `/api/v1/perfis/me` | Returns the logged-in user's data | **Token** |
| `GET` | `/api/v1/perfis/me/historico` | User's transaction history | **Token** |

---

**Developed by Mateus Domingues**
