# Bloodtestnearme (Triguna Healthcare Backend)

Bloodtestnearme is a Frappe application that provides backend features for a
blood testing and diagnostic service, including packages, orders, diagnostic
centers, and related website content.

## Features

- Manage test packages, package categories, package tags, and certifications.
- Manage online and offline orders and their related records.
- Store diagnostic center and test center address information.
- Manage blogs, offers, header sliders, job openings, and job applications.
- Generate QR codes and barcodes.
- Provide whitelisted API methods for the app's website and integrations.
- Use Frappe Desk doctypes, workspaces, dashboards, notifications, and print
  formats.

## Requirements

- A Frappe Bench environment with a Frappe version compatible with this app.
  The repository does not pin a Frappe version; use matching framework and app
  branches in your Bench.
- Python 3.10 or later.
- MariaDB and Redis, as configured for a standard Frappe Bench. The CI workflow
  currently tests with MariaDB 10.6 and Redis.
- Node.js and Yarn for building Bench assets. The CI workflow uses Node.js 18.

## Frappe app dependencies

The app's `required_apps` setting declares **Frappe** as its only required
Frappe app:

| App       | Required | Notes                                     |
| --------- | -------- | ----------------------------------------- |
| `frappe`  | Yes      | Installed and managed by Frappe Bench.    |
| `erpnext` | No       | Not declared as a dependency by this app. |

The app also installs these Python packages from `requirements.txt`:

- `qrcode[pil]` — QR code generation with Pillow image support.
- `python-barcode~=0.15.1` — barcode generation.

## Installation

From an existing Bench, fetch and install the app:

```bash
cd /path/to/frappe-bench
bench get-app https://github.com/erp-quantumberg/erpnext-healthcare-triguna-healthcare-backend.git --branch develop
bench --site your-site.example install-app bloodtestnearme
```

Replace `develop` with the branch compatible with your Frappe installation if
needed. To verify installation, run:

```bash
bench --site your-site.example list-apps
```

## Development setup

Use a Frappe Bench and site configured for the same framework branch as the
app. From the Bench directory:

```bash
bench get-app https://github.com/erp-quantumberg/erpnext-healthcare-triguna-healthcare-backend.git --branch develop
bench setup requirements
bench new-site bloodtestnearme.local
bench --site bloodtestnearme.local install-app bloodtestnearme
bench build --app bloodtestnearme
bench start
```

Run the app's automated tests with:

```bash
bench --site bloodtestnearme.local run-tests --app bloodtestnearme
```

The repository's CI workflow also installs the app on a temporary site and runs
this test command.

## Backend API

Frappe exposes whitelisted methods through `/api/method/` followed by the
method's dotted Python path. For example:

```text
GET /api/method/bloodtestnearme.api.packages.get_all_packages
GET /api/method/bloodtestnearme.api.blogs.get_blogs
GET /api/method/bloodtestnearme.api.pincodes_api.get_pincodes
```

Other API modules include `order_api`, `diagnostic_center`, `offers`,
`package_category`, `testcenter_address`, `header_slider`, `certifications`,
`jobapp`, `contact`, and `search_api`. Use the method names and arguments in
`bloodtestnearme/api/` as the source of truth for the current API.

Frappe returns method results in its standard response envelope. Use the HTTP
method appropriate to the operation and send arguments as query parameters for
GET requests or as request data for POST requests. Authentication and guest
access are controlled per method by Frappe's whitelist settings; check each
method before relying on it for a public integration. In particular, do not
assume that all endpoints are public or that a guest-accessible endpoint is
suitable for unrestricted production use.

## Configuration notes

- Install the Frappe app dependency before installing this app on a site.
- Python runtime dependencies are declared in `requirements.txt` and installed
  by Bench when setting up app requirements.
- Use the standard Frappe site configuration for database credentials, Redis,
  email, and other environment-specific settings.
- The app metadata and Frappe hooks, including `required_apps` and method
  overrides, are maintained in `bloodtestnearme/hooks.py`.
- API documentation/Swagger blueprint registration is not enabled by default
  in the current hooks configuration.

## Troubleshooting

- **App is missing from `list-apps`:** Check that `bench get-app` completed and
  that `install-app` was run against the intended site.
- **Dependency import errors:** From the Bench directory, run
  `bench setup requirements`, then restart the Bench processes.
- **Database or Redis connection errors:** Verify the site's database settings
  and that the Bench-managed MariaDB and Redis services are running.
- **Assets are missing or stale:** Run `bench build --app bloodtestnearme` and
  reload the site.
- **API method not found:** Confirm the dotted path and method name under
  `bloodtestnearme/api/`, and check that the method is whitelisted.
- **Tests fail to run:** Confirm the target site exists, the app is installed
  on it, and tests are enabled for that site.

## Contributing

Contributions are welcome. Before opening a pull request:

1. Make changes on a feature branch.
2. Run the app tests with `bench --site your-site.example run-tests --app bloodtestnearme`.
3. Install and run the configured pre-commit hooks:

   ```bash
   pip install pre-commit
   pre-commit install
   pre-commit run --all-files
   ```

The repository's checks also include Frappe Semgrep rules and a Python
dependency audit.

## License

This app is distributed under the MIT License. See [license.txt](license.txt).
