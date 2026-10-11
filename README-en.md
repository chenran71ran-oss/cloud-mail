<p align="center">
  <img src="doc/demo/logo.png" width="88" alt="Cloud Mail logo">
</p>

<h1 align="center">Cloud Mail</h1>

<p align="center">Your domain. Your mailbox.</p>
<p align="center">Self-hosted email on Cloudflare Workers, with a modern web interface, domain-based receiving and configurable sending.</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-22c55e" alt="MIT License"></a>
  <a href="https://developers.cloudflare.com/workers/"><img src="https://img.shields.io/badge/Cloudflare-Workers-f38020" alt="Cloudflare Workers"></a>
  <a href="https://vuejs.org/"><img src="https://img.shields.io/badge/Vue-3-42b883" alt="Vue 3"></a>
  <a href="https://hono.dev/"><img src="https://img.shields.io/badge/API-Hono-e36002" alt="Hono API"></a>
</p>

<p align="center">
  <a href="README.md">简体中文</a> · English
  <br>
  <a href="docs/deployment.md">Deployment</a> ·
  <a href="docs/README.md">Documentation</a> ·
  <a href="docs/troubleshooting.md">FAQ</a> ·
  <a href="CONTRIBUTING.md">Contributing</a>
</p>

> This repository is a personal fork of [maillab/cloud-mail](https://github.com/maillab/cloud-mail). [chenran71ran-oss](https://github.com/chenran71ran-oss/cloud-mail) maintains deployment configuration and documentation here. Core functionality comes from upstream; the original attribution and MIT license are preserved.

## Overview

Create mailboxes on your own domain and manage messages in a browser. The frontend and API run on one Cloudflare Worker. Incoming messages enter through Cloudflare Email Routing, with data stored in D1 and KV.

Cloud Mail suits personal domain email, small teams and self-hosting experiments. Check the quotas, pricing and domain requirements of Cloudflare and your sending provider before deploying.

## Features

| Capability | Description |
| --- | --- |
| Domain mailboxes | Multiple addresses, receiving, search, stars and attachments |
| Outbound email | Resend integration; separate domain verification and service configuration required |
| Administration | Users, roles, mailbox limits and sending quotas |
| Responsive interface | Vue 3 and Element Plus for desktop and mobile browsers |
| Notifications | Optional Telegram notifications and email forwarding |
| Automation | Open API, Workers AI verification-code extraction and statistics |
| Site settings | Custom title, login background and Turnstile integration |

Optional features require their own bindings, credentials and application settings. Successful receiving does not verify outbound delivery, AI or notifications.

## Preview

These screenshots are provided by upstream. The deployed interface depends on version and configuration.

| Preview 1 | Preview 2 |
| --- | --- |
| ![Cloud Mail preview 1](doc/demo/demo1.png) | ![Cloud Mail preview 2](doc/demo/demo2.png) |

<details>
<summary>More screenshots</summary>

| Preview 3 | Preview 4 |
| --- | --- |
| ![Cloud Mail preview 3](doc/demo/demo3.png) | ![Cloud Mail preview 4](doc/demo/demo4.png) |

</details>

## Getting started

1. Prepare a Cloudflare-managed domain, Workers, D1 and KV.
2. Follow the [deployment guide](docs/deployment.md) to configure bindings, runtime secrets and build commands.
3. Initialize the database, register the administrator and route incoming email to the Worker.
4. Send a message from an external mailbox to verify receiving before enabling optional features.

Your website and email domains may differ: `mail.example.com` for the website and `name@example.com` for email.

The fork's [wrangler.toml](mail-worker/wrangler.toml) contains the maintainer's deployment resources. Replace its domains, administrator address, D1 ID and KV ID before deploying to another account. Configure secrets through Cloudflare Secrets.

## Documentation

The detailed deployment and operations guides are currently written in Chinese.

| Topic | Guide |
| --- | --- |
| Initial deployment | [Deployment](docs/deployment.md) |
| Bindings, domains and credentials | [Configuration](docs/configuration.md) |
| Deployment and receiving errors | [Troubleshooting and FAQ](docs/troubleshooting.md) |
| Upstream updates and backups | [Maintenance](docs/maintenance.md) |
| Issues and pull requests | [Contributing](CONTRIBUTING.md) |
| Security reports | [Security](SECURITY.md) |

See [upstream documentation](https://doc.skymail.ink/) for additional integrations. The [upstream demo](https://skymail.ink) is operated by upstream; do not use it for private messages.

## Repository layout

| Path | Purpose |
| --- | --- |
| [mail-worker/](mail-worker/) | Hono API, email processing, Drizzle and Wrangler configuration |
| [mail-vue/](mail-vue/) | Vue 3, Vite, Element Plus and frontend pages |
| [docs/](docs/) | Fork deployment and operations documentation |
| [doc/](doc/) | Upstream screenshots and images |
| [.github/](.github/) | Deployment workflow and contribution templates |

Cloudflare Workers hosts the application. D1 stores structured data; KV handles caching and default attachment storage. R2 is optional. Receiving and sending are separate flows; see [configuration](docs/configuration.md).

## Contributing and attribution

Report fork-specific issues through [this repository's issue tracker](https://github.com/chenran71ran-oss/cloud-mail/issues). Read [CONTRIBUTING.md](CONTRIBUTING.md) before submitting changes.

Thanks to the original authors and contributors of [maillab/cloud-mail](https://github.com/maillab/cloud-mail). Upstream community and support links are available in the [upstream documentation](https://doc.skymail.ink/).

## License

[MIT License](LICENSE), retaining the original `Copyright (c) 2025 aslost` notice. Include the license and copyright notice when redistributing.
