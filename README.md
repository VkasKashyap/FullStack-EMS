# Full Stack Employee Management System (MERN)

A full-stack Employee Management System built with the MERN stack. The
application provides separate access for administrators and employees
and brings employee records, attendance, leave requests, and payslips
into one place.

## Features

### Authentication and access control

-   Login for administrator and employee roles
-   JSON Web Token (JWT) authentication
-   Password hashing and verification with `bcrypt`
-   Protected API routes and administrator-only operations
-   Profile management and password change

### Administrator

-   Create, view, update, and deactivate employee records
-   View employee and attendance information
-   Review leave applications and approve or reject requests
-   Generate payslips using employee salary details
-   View dashboard summaries

### Employee

-   View personal dashboard and profile
-   Clock in and clock out
-   View attendance history and statistics
-   Submit leave applications and view their status
-   View payslips

### Additional capabilities

-   MongoDB data persistence with Mongoose models
-   React Router-based navigation
-   Axios API client with automatic Bearer-token attachment
-   Email configuration using Nodemailer
-   Inngest integration for background workflows

> Available functionality and access depend on the current
> implementation and environment configuration. Configure external
> services before testing features that rely on email or background
> workflows.

## Tech Stack

  -----------------------------------------------------------------------
  Layer                               Technologies
  ----------------------------------- -----------------------------------
  Frontend                            React, Vite, React Router, Tailwind
                                      CSS, Axios, Lucide React, React Hot
                                      Toast

  Backend                             Node.js, Express.js

  Database                            MongoDB, Mongoose

  Authentication                      JWT, bcrypt

  Background workflows                Inngest

  Email                               Nodemailer

  Development tools                   npm, Nodemon, ESLint, Git, GitHub
  -----------------------------------------------------------------------

## Project Structure

``` text
FullStack-EMS/
├── client/
│   ├── public/
│   ├── src/
│   │   ├── api/                 # Axios client and auth token interceptor
│   │   ├── components/          # Forms, dashboards, and reusable UI
│   │   ├── context/             # Authentication context
│   │   ├── pages/               # Dashboard, employees, attendance, leave, payslips, settings
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── .env                     # Create locally; do not commit
│   └── package.json
│
├── server/
│   ├── config/                  # MongoDB and email configuration
│   ├── constants/               # Shared constants such as departments
│   ├── controllers/             # Request handling and business logic
│   ├── inngest/                 # Background workflow definitions
│   ├── middleware/              # Authentication and role checks
│   ├── models/                  # User, Employee, Attendance, Leave, Payslip
│   ├── routes/                  # API routes
│   ├── seed.js                  # Initial administrator seeding
│   ├── server.js                # Express application entry point
│   ├── .env                     # Create locally; do not commit
│   └── package.json
│
└── README.md
```

## Prerequisites

Install the following before running the project:

-   [Node.js](https://nodejs.org/) and npm
-   A MongoDB database, either local MongoDB or [MongoDB
    Atlas](https://www.mongodb.com/atlas)
-   Git
-   Optional, depending on the features you test: Inngest and SMTP/email
    credentials

## Getting Started

### 1. Clone the repository

``` bash
git clone https://github.com/VkasKashyap/FullStack-EMS.git
cd FullStack-EMS
```

### 2. Install backend dependencies

``` bash
cd server
npm install
```

Create a `server/.env` file with the following variables. Replace each
placeholder with your own values:

``` dotenv
PORT=4000
MONGODB_URI=mongodb+srv://<username>:<password>@<cluster-url>/<database-name>
JWT_SECRET=replace_with_a_long_random_secret

# Email configuration (needed for email functionality)
SMTP_USER=your_smtp_username
SMTP_PASS=your_smtp_password
SENDER_EMAIL=your_verified_sender_email

# Inngest configuration (needed for configured background workflows)
INNGEST_EVENT_KEY=your_inngest_event_key
INNGEST_SIGNING_KEY=your_inngest_signing_key

# Email address used by the seed script for the initial administrator
ADMIN_EMAIL=admin@example.com
```

**Important environment notes** - Do not use the example values in a
real deployment. - Use a strong, unique `JWT_SECRET`. - In MongoDB
Atlas, ensure the database user credentials are correct and your current
IP is allowed in Network Access. - If your MongoDB password contains
special characters, URL-encode them in the connection URI. - Keep
`SMTP_PASS`, Inngest keys, `JWT_SECRET`, and `MONGODB_URI` private. -
The server expects `MONGODB_URI` and `JWT_SECRET`. Email and Inngest
settings are required only for features that use those services.

### 3. Configure the frontend

In a second terminal, go to the client directory:

``` bash
cd client
npm install
```

Create `client/.env`:

``` dotenv
VITE_BASE_URL=http://localhost:4000
```

`VITE_BASE_URL` is the backend base URL. The frontend Axios client
appends `/api` automatically, so use `http://localhost:4000`, not
`http://localhost:4000/api`.

Vite exposes variables prefixed with `VITE_` to the browser. Never put
secrets in `client/.env`.

### 4. Seed the administrator account

After the server environment is configured and MongoDB is reachable,
run:

``` bash
cd server
npm run seed
```

Use the administrator credentials created by the seed script and follow
any project-specific password setup instructions. Do not assume a
default password unless it is explicitly defined in the current seed
script.

If seeding fails with an authentication error, check the MongoDB
username and password, URL-encode special characters in the URI, confirm
the database user's permissions, and check MongoDB Atlas Network Access.

### 5. Start the backend

From `server/`:

``` bash
npm run server
```

The development server uses Nodemon and listens on port `4000` by
default (or the `PORT` value configured in `server/.env`). You can also
use:

``` bash
npm start
```

Check the root endpoint at `http://localhost:4000/`. It should respond
that the server is running once startup and database connection have
succeeded.

### 6. Start the frontend

In a separate terminal, from `client/`:

``` bash
npm run dev
```

Vite prints the local frontend URL in the terminal, usually
`http://localhost:5173`.

### 7. Build the frontend

From `client/`:

``` bash
npm run build
npm run preview
```

To run the frontend lint checks:

``` bash
npm run lint
```

## API Overview

All API routes are mounted under `/api`.

  -------------------------------------------------------------------------------------
  Method            Endpoint                      Access            Purpose
  ----------------- ----------------------------- ----------------- -------------------
  `POST`            `/api/auth/login`             Public            Authenticate a user

  `GET`             `/api/auth/session`           Authenticated     Validate the
                                                                    current session

  `POST`            `/api/auth/change-password`   Authenticated     Change the current
                                                                    user's password

  `GET`             `/api/dashboard`              Authenticated     Retrieve dashboard
                                                                    information

  `GET`             `/api/employees`              Admin             List employees

  `POST`            `/api/employees`              Admin             Create an employee

  `PUT`             `/api/employees/:id`          Admin             Update an employee

  `DELETE`          `/api/employees/:id`          Admin             Deactivate/remove
                                                                    an employee
                                                                    according to
                                                                    controller logic

  `GET`             `/api/profile`                Authenticated     Retrieve the
                                                                    current user's
                                                                    profile

  `POST`            `/api/profile`                Authenticated     Update the current
                                                                    user's profile

  `POST`            `/api/attendance`             Authenticated     Clock in or clock
                                                                    out

  `GET`             `/api/attendance`             Authenticated     Retrieve attendance
                                                                    records

  `POST`            `/api/leave`                  Authenticated     Submit a leave
                                                                    application

  `GET`             `/api/leave`                  Authenticated     Retrieve leave
                                                                    applications

  `PATCH`           `/api/leave/:id`              Admin             Approve or reject a
                                                                    leave application

  `POST`            `/api/payslips`               Admin             Generate a payslip

  `GET`             `/api/payslips`               Authenticated     Retrieve payslips

  `GET`             `/api/payslips/:id`           Authenticated     Retrieve a
                                                                    particular payslip

  `GET`             `/api/inngest`                Inngest           Inngest
                                                  integration       event/function
                                                                    endpoint
  -------------------------------------------------------------------------------------

Protected endpoints require the token in the request header:

``` http
Authorization: Bearer <your-jwt-token>
```

For exact request and response schemas, refer to the route and
controller files in `server/`.

## How It Works

1.  A user signs in through the React frontend.
2.  The frontend sends the credentials to the Express API using Axios.
3.  The backend verifies the user and password, then issues a JWT when
    authentication succeeds.
4.  The frontend attaches the token to later API requests.
5.  Authentication middleware verifies the token; administrator-only
    routes also check the user's role.
6.  Controllers use Mongoose models to read or update MongoDB records.
7.  The API returns a response, and React updates the interface.

## Deployment Notes

The repository includes Vercel configuration files for both the client
and server. Deployment still requires configuring environment variables
and ensuring the frontend points to the deployed backend.

1.  Deploy the `server` as a backend service and configure its
    environment variables.
2.  Deploy the `client` as a frontend application.
3.  Set `VITE_BASE_URL` to the deployed backend's origin, without `/api`
    at the end.
4.  Configure MongoDB Atlas Network Access and database credentials for
    the deployed backend.
5.  Configure CORS to allow only the frontend origins you intend to
    support.
6.  Configure SMTP and Inngest credentials if those integrations are
    used.
7.  Test login, employee operations, attendance, leave management, and
    payslip generation on the deployed application.

The presence of Vercel configuration does not by itself guarantee that
every feature is production-ready; verify deployment settings and test
the complete application after deployment.

## Security

-   Never commit `.env` files, credentials, tokens, or private keys.
-   If a real secret has been pushed to GitHub, rotate it; deleting it
    from the latest commit alone may not remove it from Git history.
-   Keep all database credentials and JWT secrets on the backend.
-   Use HTTPS in production and configure CORS deliberately.
-   Validate and authorize requests on the server; frontend route
    protection alone is not sufficient.
-   Use non-production test accounts and data when demonstrating the
    project.

## Troubleshooting

### MongoDB authentication failed

-   Re-check `MONGODB_URI`, database username, and password.
-   URL-encode special characters in the password.
-   Confirm the database user has appropriate permissions.
-   Check Atlas Network Access and ensure the server's IP is allowed.
-   Restart the backend after changing environment variables.

### Frontend cannot reach the backend

-   Confirm the backend is running and the `PORT` is correct.
-   Check `client/.env` and ensure `VITE_BASE_URL` is the backend
    origin, without `/api`.
-   Restart the Vite server after editing environment variables.
-   Check browser developer tools for failed requests and CORS errors.

### Login or protected routes fail

-   Verify that the seed operation completed successfully and the user
    exists.
-   Check that the frontend sends `Authorization: Bearer <token>`.
-   Confirm `JWT_SECRET` is configured consistently for the running
    backend.
-   Inspect server logs without exposing passwords, tokens, or
    connection strings.

## Future Improvements

-   Add automated unit, integration, and end-to-end tests.
-   Add request validation and consistent centralized error handling.
-   Improve audit logging for administrative changes.
-   Add pagination and filtering for large employee lists.
-   Review production session/token storage and security controls.
-   Add deployment health checks and structured logging.

## Author

**Vikas Kashyap**

-   GitHub: [@VkasKashyap](https://github.com/VkasKashyap)
-   Project repository:
    [FullStack-EMS](https://github.com/VkasKashyap/FullStack-EMS)

------------------------------------------------------------------------

If you find this project useful for learning, feel free to explore the
code and suggest improvements.
