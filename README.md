# setup-trellis-cli

[![Build status](https://img.shields.io/github/actions/workflow/status/roots/setup-trellis-cli/test.yml?branch=main&style=flat-square)](https://github.com/roots/setup-trellis-cli/actions)
![GitHub release](https://img.shields.io/github/release/roots/setup-trellis-cli?style=flat-square)

The `roots/setup-trellis-cli` action is a JavaScript action that sets up [Trellis CLI](https://github.com/roots/trellis-cli) in your GitHub Actions workflow by:

* Downloading a specific version of trellis-cli (defaults the latest) and adding it to the `PATH`.
* Creating a `.vault_pass` file with your Ansible Vault password input.
* Initializing the [Trellis](https://github.com/roots/trellis) project in the GitHub repo by running the `trellis init` command.
    * Creates a virtual environment and installs dependencies (mainly Ansible) with automatic caching.
    * Installs Ansible galaxy roles by running `trellis galaxy install` with automatic caching.
    
See the [**Deploying Trellis WordPress Sites with GitHub Actions guide**](https://roots.io/trellis/docs/deploy-with-github-actions/).

## Example usage

```yaml
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v2
- uses: roots/setup-trellis-cli@v1
  with:
    ansible-vault-password: ${{ secrets.ANSIBLE_VAULT_PASSWORD }}
    repo-token: ${{ secrets.GITHUB_TOKEN }}
- run: trellis deploy production
```

See the [examples](./examples) for some full workflow examples including a site
with a [Sage](https://github.com/roots/sage)-based theme and a project
with a [multi-repo](https://github.com/roots/trellis/issues/883#issuecomment-329054858) [setup](https://github.com/roots/trellis/issues/883#issuecomment-329052189).

See [Workflow syntax for GitHub Actions](https://help.github.com/en/articles/workflow-syntax-for-github-actions) for more details on writing GitHub workflows.

## Setup

## Inputs

#### `ansible-vault-password`
**Required** Ansible Vault password. Use a [GitHub secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets) for this value (example in usage
above).

This can also be set using the GitHub CLI:

```bash
gh secret set ANSIBLE_VAULT_PASSWORD -b $(cat trellis/.vault_pass)
```

Note: this is a required input even if you don't use Ansible Vault. Just set
this to any random placeholder string.

#### `repo-token`
Optionally set the GitHub token for API authorization. Setting this token will avoid any potential API rate limits.

The best option is to set this to the default token secret which GitHub automatically sets: `secrets.GITHUB_TOKEN`.

See https://docs.github.com/en/actions/security-guides/automatic-token-authentication

#### `auto-init`
Whether to automatically run the `trellis init` command after install.

**Default**: `true`

If you want to manage dependencies manually yourself, disable this option.

#### `cache-virtualenv`
When enabled, the virtualenv created by the `trellis init` command is automatically
cached.

**Default**: `true`

#### `galaxy-install`
Whether to automatically run the `trellis galaxy install` command to install
Ansible Galaxy roles.

**Default**: `true`

#### `trellis-directory`
Path to the Trellis project directory. This defaults to `trellis` to match the default directory structure of a project created with `trellis new`.

**Default**: `trellis`

#### `version`
Version of Trellis CLI to install. See
[Releases](https://github.com/roots/trellis-cli/releases) for all possible
versions.

Note: if you want a specific version, include the 'v' in the version name (eg:
`v1.5.1`).

**Default**: `latest`

## SSH known hosts
Most usages of this action will require SSH known hosts to be set, including the example workflow which uses `shimataro/ssh-key-action`.

Since the GitHub Action runner will be the client SSHing into your remote Trellis server, this is needed to allow a connection from GitHub -> your server, which means the known host is for the remote server hostname.

This value is _not_ just the hostname/IP, it needs be in OpenSSH format which looks like this:

```plain
example.com ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0SdG6UOoqKLsabgH5C9okWi0dh2l9GKJl
```

Or the hashed output:
```plain
|1|nLf9avvc+tz8nFgUW/3tPwjTA4Q=|dLZn1guXUrBjLg4s23ird724guA= ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOMqqnkVzrm0SdG6UOoqKLsabgH5C9okWi0dh2l9GKJl
```

There's a few ways to get this value:

1. using trellis-cli:
```bash
trellis key generate
```
2. manually using `ssh-keyscan`:
```bash
ssh-keyscan -t ed25519 -H MY_SERVER_HOSTNAME
```
3. from your `~/.ssh/known_hosts` file (if you've previously SSH'd into the server):

Note: always use a GitHub secret to store this value. Do not hardcode the plain
text value in your workflow file. `trellis key generate` will use a secret
automatically.

## Outputs

#### `version`
The Trellis CLI version installed. Example: `v1.5.1`

## Contributing

Contributions are welcome from everyone. We have [contributing guidelines](https://github.com/roots/guidelines/blob/master/CONTRIBUTING.md) to help you get started.

## Community

Keep track of development and community news.

- Join us on Roots Slack by becoming a [GitHub sponsor](https://github.com/sponsors/roots) or [patron](https://www.patreon.com/rootsdev)
- Participate on the [Roots Discourse](https://discourse.roots.io/)
- Follow [@rootswp on Twitter](https://twitter.com/rootswp)
- Read and subscribe to the [Roots Blog](https://roots.io/blog/)
- Subscribe to the [Roots Newsletter](https://roots.io/subscribe/)
