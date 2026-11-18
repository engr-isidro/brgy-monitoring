# Barangay Report System

This project contains the XAMPP PHP/MySQL edition and a separate Netlify edition. The XAMPP edition is the existing local app. The Netlify edition uses a static JavaScript interface, Netlify Functions, and Supabase Auth/Postgres/Storage; it does not execute the PHP app.

## Important location-data note

The project owner supplied and confirmed 50 official Cluster choices for San Isidro, San Jose, San Rafael, Geronimo, Balite, Rosario, and Manggahan. Those entries are in `database/database.sql` and the current XAMPP database. Cluster entries are still missing for Burgos, Macabud, Mascap, and Puray, so the Cluster field remains optional until their official names are provided. Purok is not part of this system.

## Requirements

- XAMPP with PHP 8+ and MySQL/MariaDB
- PHP extensions enabled: `pdo_mysql` and `fileinfo`
- Modern browser with internet access for Bootstrap, Bootstrap Icons, Chart.js and Google Fonts CDN assets

## XAMPP setup

1. Copy this folder into `C:\xampp\htdocs\barangaywatch`.
2. Start Apache and MySQL in XAMPP Control Panel.
3. Open `http://localhost/phpmyadmin`, create/import by selecting **Import** and loading `database/database.sql`. The SQL creates/uses the `barangaywatch` database and inserts the barangays, categories, and currently confirmed Cluster choices. It uses `CREATE TABLE IF NOT EXISTS` and does not drop existing tables.
   If you already have an older BarangayWatch database, import `database/migrations/20261115_remove_purok.sql` once to remove the former Purok table/columns. The current XAMPP database has already been migrated.
4. Review `config/config.php`. The included blank-password `root` setting matches a common local XAMPP installation only. Set credentials appropriate for your local MySQL instance. Do not expose this configuration to the internet.
5. Ensure `uploads/` is writable by Apache. The folder denies script execution with `.htaccess`.
6. Open `http://localhost/barangaywatch/?a=setupadmin` on the XAMPP computer. Complete the one-time form to create the first Super Admin. This setup link is available only from the local computer and disappears after an administrator account exists.
7. Sign in with that account. Open **Admin accounts** and create the Barangay Admin, enter the official barangay assignment, and set a password for that account.
8. Sign out, then sign in with the Barangay Admin account to open the administrator dashboard and review the report queue. Residents can register separately; resident sign-up never accepts an administrator role.

For command-line provisioning, `php tools/create_admin.php` remains available. It prompts for the password instead of storing a default credential in source code.

## Netlify setup

See [`documentation/NETLIFY.md`](documentation/NETLIFY.md). The Netlify edition is in `netlify-site/`, with a protected administrator function in `netlify/functions/`. It requires a Supabase project and three Netlify environment variables. A direct static drag-and-drop deploy is not enough to build the function or inject the public config; use a Git-connected deploy or Netlify CLI.

## Seed IDs

The SQL inserts in the listed order; a fresh database assigns IDs 1–11: Balite, Burgos, Geronimo, Macabud, Manggahan, Mascap, Puray, Rosario, San Isidro, San Jose, and San Rafael. Verify IDs in phpMyAdmin before using the CLI setup.

## Role and access notes

- Resident queries are scoped to the authenticated user. Report details check report ownership on the server.
- Barangay Admin report-list, dashboard, and detail queries apply the assigned barangay on the server.
- Only Super Admin can change admin roles/activation, categories, or official location records.
- Report references are random, unique database-constrained identifiers. They are not public lookup tokens; a resident must authenticate and the report owner is checked.
- Passwords use `password_hash`/`password_verify`, PDO uses native prepared statements, state-changing forms use CSRF tokens, and uploads are MIME/content checked and randomly named.

## Project map

- `index.php` – XAMPP PHP application routes and HTML views
- `includes/app.php` – PDO, escaping, selectors, common view helpers
- `config/config.php` – local database settings (Apache access denied)
- `database/database.sql` – schema and verified fixed seeds
- `assets/` – stylesheet and location selector JavaScript
- `uploads/` – protected evidence storage
- `tools/create_admin.php` – optional CLI administrator provisioning
- `documentation/` – capstone chapters, diagrams, data dictionary, user manual and verification status
- `netlify-site/` and `netlify/functions/` – separate Netlify/Supabase edition
- `database/supabase-netlify.sql` – hosted Postgres schema and location/category seeds

## Scope and verification status

The implementation and its verified/pending status are documented in [`documentation/IMPLEMENTATION.md`](documentation/IMPLEMENTATION.md). The system has been linted and exercised locally against XAMPP PHP/MariaDB for the checks listed in `documentation/TESTING.md`; this does not constitute a production deployment or complete security review. Have the adviser review methodology, references, and study-specific content before submission.

For production use, add HTTPS, rate limiting, backups, audit retention rules, privacy notices/consent review, server-side upload size limits, deployment secrets management, and a full security review.
