# Pretix

Pretix is a production-ready ticketing platform for conferences, festivals, concerts, exhibitions, workshops, and other events. It provides event organizers with tools for ticket sales, attendee management, payments, invoicing, check-in, reporting, and extensibility through plugins and APIs.

This repository contains the core Pretix application, including its Django backend, organizer control interface, public ticket shop, REST API, plugin framework, background tasks, frontend assets, deployment files, documentation, and automated tests.

## Overview

Pretix supports both single events and recurring event series. Organizers can configure products, quotas, pricing, payment methods, attendee questions, order workflows, ticket documents, and access-control rules from a centralized administration interface.

The application is designed for:

- Public and private ticket sales
- Conferences, concerts, festivals, and exhibitions
- Multi-date and recurring events
- Free, paid, and donation-based registration
- Custom event storefronts
- On-site and remote attendee check-in
- Third-party integrations and custom plugins

## Features

- Organizer, team, and permission management
- Single events and event series
- Products, variations, quotas, bundles, and add-ons
- Flexible pricing, fees, vouchers, and discounts
- Configurable checkout and attendee questions
- Order creation, modification, cancellation, and refund workflows
- Multiple payment-provider integrations
- Invoices, receipts, and downloadable ticket documents
- Waiting lists and availability controls
- Email communication and scheduled sending rules
- Check-in lists, admission validation, and device integration
- Multilingual event shops and administration interfaces
- Custom domains and organizer-specific branding
- REST API with token and OAuth authentication
- Webhooks and asynchronous event processing
- Extensible plugin architecture
- Reporting, exports, and operational statistics
- Security controls, two-factor authentication, and WebAuthn support

## Tech Stack

| Area | Technologies |
| --- | --- |
| Language | Python 3.11 or later |
| Backend | Django 5.2 |
| API | Django REST Framework |
| Frontend | Vue 3, JavaScript, TypeScript |
| Asset tooling | Vite, Sass, Stylus, Pug |
| Background processing | Celery, Kombu |
| Database | PostgreSQL for production |
| Cache and broker | Redis |
| Web server interface | WSGI |
| Authentication | Django authentication, OAuth, OTP, WebAuthn |
| Payments | Stripe, PayPal, and plugin-based providers |
| Documents | ReportLab, Pillow, QR code generation |
| Testing | Pytest, pytest-django, Playwright |
| Quality tooling | Flake8, isort, ESLint |
| Packaging | setuptools, pip |
| Deployment | Docker, Nginx, Supervisor |
| Documentation | Sphinx |

## Project Structure

```text
pretix/
├── .github/                    Repository automation and workflows
├── _build/                     Custom Python build backend
├── deployment/
│   └── docker/                 Container runtime and service configuration
├── doc/                        Developer and administrator documentation
├── res/                        Shared project resources
├── src/
│   ├── pretix/
│   │   ├── api/                REST API endpoints and serializers
│   │   ├── base/               Core models, services, and business logic
│   │   ├── control/            Organizer administration interface
│   │   ├── helpers/            Shared framework utilities
│   │   ├── locale/             Translation resources
│   │   ├── multidomain/        Custom-domain handling
│   │   ├── plugins/            Built-in plugins and extension interfaces
│   │   ├── presale/            Public event shop and checkout flow
│   │   ├── static/             Frontend scripts, styles, and assets
│   │   ├── celery_app.py       Celery application setup
│   │   ├── settings.py         Django settings loader
│   │   └── urls.py             Application route configuration
│   ├── tests/                  Backend and integration tests
│   ├── manage.py               Django management entry point
│   └── Makefile                Development and asset commands
├── Dockerfile                  Production container build
├── package.json                Frontend dependencies and scripts
├── pyproject.toml              Python metadata and dependencies
├── setup.cfg                   Python tooling configuration
├── tsconfig.json               TypeScript configuration
└── vite.config.ts              Frontend build configuration
```

## Installation

The following instructions create a local development environment from source.

### Requirements

- Python 3.11 or later
- pip and Python virtual-environment support
- Node.js 24
- npm
- Git
- gettext
- Development headers for OpenSSL, libffi, libxml2, and libxslt
- PostgreSQL and Redis when testing production-style configuration

### Create a Virtual Environment

```bash
python3 -m venv env
source env/bin/activate
python -m pip install --upgrade pip setuptools
```

On Windows PowerShell, activate the environment with:

```powershell
env\Scripts\Activate.ps1
```

### Install Python Dependencies

```bash
pip install -e ".[dev]"
```

### Install Frontend Dependencies

```bash
cd src
make npminstall
```

### Prepare Static Assets and Database

From the `src` directory, run:

```bash
python manage.py collectstatic --noinput
python manage.py migrate
```

The development setup creates an initial administrator account:

```text
Email: admin@localhost
Password: admin
```

These credentials are intended only for local development.

### Start the Development Server

```bash
python manage.py runserver
```

Open the `/control/` path on the local server to access the organizer interface.

The Django development server also starts the Vite development process required by control-panel Vue components.

### Start Optional Background Processing

Celery is not required for the simplest local setup. When Celery is configured, start a worker separately:

```bash
celery -A pretix.celery_app worker -l info
```

Run periodic tasks manually with:

```bash
python manage.py runperiodic
```

### Develop the Ticket Widget

Start the separate widget development server with:

```bash
npm run dev:widget
```

## Usage

1. Sign in to the organizer control interface.
2. Create an organizer account.
3. Create an event or event series.
4. Configure products, prices, quotas, and sales periods.
5. Add attendee questions and checkout settings.
6. Enable the required payment methods.
7. Configure ticket documents, email templates, and check-in lists.
8. Test the complete order and payment workflow.
9. Publish the event shop when configuration is complete.
10. Use the REST API, webhooks, or plugins for external integrations.

Rebuild production frontend assets with:

```bash
npm run build
```

Compile translation files with:

```bash
make localecompile
```

Update frontend assets used by customized shops with:

```bash
python -m pretix collectstatic --noinput
python -m pretix updateassets
```

## Configuration

Pretix reads application configuration from `pretix.cfg`. A development configuration can be placed inside the `src` directory.

A typical production configuration includes the following areas:

| Section | Purpose |
| --- | --- |
| `pretix` | Instance identity, data directory, currency, and application settings |
| `database` | PostgreSQL connection and pooling settings |
| `redis` | Cache and shared-state connection |
| `celery` | Broker and result-backend configuration |
| `mail` | SMTP delivery and sender settings |
| `filesystem` | Media, ticket, export, and generated-file storage |
| `logging` | Log targets, levels, and error reporting |
| `security` | Trusted hosts, HTTPS, cookies, and proxy handling |

Keep credentials, secret keys, payment-provider secrets, and SMTP passwords outside version control.

Production environments should use:

- PostgreSQL instead of a development database
- Redis for caching and task coordination
- Dedicated Celery workers
- A periodic-task scheduler
- Persistent media and data storage
- HTTPS behind a reverse proxy
- Regular database and file backups

## Contributing

Discuss significant new features before beginning implementation. Bug fixes and small improvements can be submitted directly with focused tests.

Install all development dependencies:

```bash
pip install -e ".[dev]"
cd src
make npminstall
```

Run backend checks:

```bash
flake8 .
isort -c .
python manage.py check
```

Run the test suite:

```bash
pytest
```

Run tests in parallel when appropriate:

```bash
pytest -n auto
```

Run frontend linting:

```bash
npm run lint:eslint
```

Before submitting a pull request:

- Add tests for new behavior and regression fixes
- Include migrations for database-model changes
- Mark all user-facing text for translation
- Follow existing Django and project conventions
- Keep API behavior backward-compatible where possible
- Update documentation for user-facing changes
- Keep frontend and generated assets synchronized
- Ensure all automated checks pass
