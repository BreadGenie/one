# Local setup with Pilot

Use `develop` for Framework and all apps in this workspace. Check the
app’s `pyproject.toml` and `hooks.py` for its current dependencies.

## App worktrees

Run `pilot dev` from the app worktree you’re editing. Pilot links that
checkout into its own bench. See `pilot dev --help` for commands.

## Create the shared development environment

With Pilot installed, run these commands from your development workspace.
In the first-start wizard choose `https://github.com/frappe/frappe.git`
and explicitly select `develop`. Leave the development processes running.
See [Pilot prerequisites and setup](https://github.com/frappe/pilot#requirements).

```sh
pilot new frappe-one --frappe-branch develop --database mariadb
pilot -b frappe-one start
```

## Install the apps

After initialization, use a second terminal. The following Bash commands
target a fresh local site. They fetch each app into the Pilot environment,
then install the apps on the site in dependency order. Helpdesk needs
MariaDB for installation.
See the [Pilot command reference](https://github.com/frappe/pilot/blob/develop/docs/commands.md).

```bash
(
  set -e
  apps=(
  erpnext payments telephony hrms lending education
  lms webshop helpdesk suite raven gameplan
  wiki blog builder crm insights whatsapp
  )
  for app in "${apps[@]}"; do
    pilot -b frappe-one get-app "https://github.com/frappe/$app.git" --branch develop
  done
  pilot -b frappe-one new-site one.localhost
  pilot -b frappe-one install-app one.localhost "${apps[@]}"
  pilot -b frappe-one list-site-apps one.localhost
)
```

## App dependencies

| Install first | Dependent apps |
|---|---|
| ERPNext | HR, Lending, Education, Webshop |
| Payments | Learning, Webshop |
| Telephony | Helpdesk |

Suite Meet calls require a [media server](https://github.com/frappe/suite/tree/develop/suite/meet/sfu-server).
Incoming WhatsApp webhooks require a [public HTTPS endpoint](https://github.com/frappe/whatsapp/tree/develop#readme).
