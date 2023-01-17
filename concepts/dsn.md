---
layout: default
title: Data Source Name
parent: Concepts
nav_order: 1
git_write_benefits: >-
    Write access allows creating the store.yml during create project, and adding a
    migration script stub. However, both can be created manually
---

# Data Source Names (DSN)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}
----

Cinch uses data source names to represent connection strings for the [target]({% link concepts/target.md %}) database,
[history]({% link concepts/history.md %}) database and the [migration store]({% link concepts/migration-store.md %}).

## Format
The cinch DSN format is a space-separated list of parameters: `name=value` pairs ignoring spaces around the `=` sign.
Every DSN must contain a `driver` parameter.

```text
driver=pgsql host=localhost port = 333
```

If a value contains a space, it must be single quoted. Within single quotes, backslash `\` and single quote `'` must be
escaped: `'ex\\amp\'le'`. 

Values that are not quoted, are not escaped. A value is only considered quoted if it begins and ends with a single quote.

```text
name = 'value'      # value
name = 'value       # 'value
name = value'       # value'
name = '\'value\''  # 'value'
```

## Database Parameters
Database DSNs are used by the [target]({% link concepts/target.md %}) and [history]({% link concepts/history.md %}) databases.
Below is a list of all database parameters, along with their database-specific defaults:

| name            | MySQL/MariaDB | PostgreSQL | Azure DB/SQL Server | SQLite   |
|-----------------|---------------|--|------------------|----------|
| driver          | <span style="color:#FF595E">mysql</span> | <span style="color:#FF595E">pgsql</span> | <span style="color:#FF595E">mssql</span>            | <span style="color:#FF595E">sqlite</span>   |
| user            | root          | postgres | sa            | <span style="color:gray">_n/a_</span>      |
| password        | <span style="color:gray">_empty_</span>  | <span style="color:gray">_empty_</span> | <span style="color:gray">_empty_</span>      | <span style="color:gray">_n/a_</span>      |
| host        | 127.0.0.1     | 127.0.0.1 | 127.0.0.1        | <span style="color:gray">_n/a_</span>      |
| port            | 3306          | 5432 | 1443              | <span style="color:gray">_n/a_</span>      |
| dbname[^1]          | <span style="color:#FF595E">_required_</span>      | <span style="color:#FF595E">_required_</span> | <span style="color:#FF595E">_required_</span>         | <span style="color:#FF595E">_required path_</span>      |
| charset         | utf8mb4       | UTF8 | UTF8             | <span style="color:gray">_n/a_</span>      |
| search_path[^2] | <span style="color:gray">_n/a_</span>           | <span style="color:gray">_empty_</span> | <span style="color:gray">_n/a_</span>              | <span style="color:gray">_n/a_</span>      |
| sslmode[^3]     | <span style="color:gray">_n/a_</span>           | prefer | <span style="color:gray">_n/a_</span>              | <span style="color:gray">_n/a_</span>      |

All [optional parameters](#optional-parameters) are supported.

[^1]: DB DSN `dbname`: database name or path to sqlite database file
[^2]: DB DSN `search_path`: comma-separated list of postgres schemas: such as `billing,account,user`
[^3]: DB DSN `sslmode`: disable, allow, prefer, require, verify-ca, verify-full - see [PostgreSQL parameters](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-PARAMKEYWORDS){:target="_blank"} for details

## Migration Store Parameters
All migration store DSNs use the below parameters. The `token` parameter defaults to an environment variable. 

| name       | description                                                                    | Filesystem                                    | GitHub                                        | GitLab                                        | Azure                                         |
|------------|--------------------------------------------------------------------------------|-----------------------------------------------|-----------------------------------------------|-----------------------------------------------|-----------------------------------------------|
| driver     | name of driver                                                                 | <span style="color:#FF595E">fs</span>         | <span style="color:#FF595E">github</span>     | <span style="color:#FF595E">gitlab</span>     | <span style="color:#FF595E">azure</span>      |
| store_dir  | relative/absolute path for filesystem, relative to repo root for git providers | <span style="color:#FF595E">_required_</span> | <span style="color:#FF595E">_required_</span> | <span style="color:#FF595E">_required_</span> | <span style="color:#FF595E">_required_</span> |
| owner      | github user or organization name                                               | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         |
| project_id | located at the top of gitlab project page                                      | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> | <span style="color:gray">_n/a_</span>         |
| org        | azure devops organization name                                                 | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> |
| project    | azure devops project name                                                      | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> |
| repo       | git repository name                                                            | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> |
| branch     | branch within repository                                                       | <span style="color:gray">_n/a_</span>         | <span style="color:#FF595E">_required_</span> | <span style="color:#FF595E">_required_</span> | <span style="color:#FF595E">_required_</span> |
| token      | personal access token (read and write)                                         | <span style="color:gray">_n/a_</span>         | `CINCH_GITHUB_TOKEN`                          | `CINCH_GITLAB_TOKEN`                          | `CINCH_AZURE_TOKEN`                           |
| host       | gitlab on-premise host/IP                                                      | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | gitlab.com                                    | <span style="color:gray">_n/a_</span>         |
| port       | gitlab on-premise port                                                         | <span style="color:gray">_n/a_</span>         | <span style="color:gray">_n/a_</span>         | 443                                           | <span style="color:gray">_n/a_</span>         |

All [optional parameters](#optional-parameters) are supported.

## Optional Parameters

The below table lists the optional parameters for all data sources.

| name            | default | description                                |
|-----------------|---------|--------------------------------------------|
| connect_timeout | 10      | connect timeout in seconds                 |
| timeout         | 10000   | request/query timeout in milliseconds      |
| sslca           | empty   | path to certificate authority              |
| sslcert         | empty   | path to client certificate                 |
| sslkey          | empty   | path to private key for client certificate |

{: .note }
These parameters are ignored when used with a Filesystem or SQLite driver: `driver=fs` or `driver=sqlite`.




{% comment %}
## Filesystem DSN

`driver=fs`

A filesystem DSN references an absolute or relative pathname.

## MySQL DSN

The MySQL DSN uses a `mysql` scheme. The format is identical to [pgsql](#postgresql-dsn).

### Format

```bash
mysql://user:password@host:port/dbname?charset=name
```

### Defaults

| value    | default                            |
|----------|------------------------------------|
| user     | root - ex: `mysql://:password@...` |
| password | empty                              |
| host     | 127.0.0.1 - ex: `mysql:dbname`     |
| port     | 3306                               |
| dbname   | required value, no default         |
| charset  | utf8mb4                            |

## PostgreSQL DSN

The PostgreSQL DSN uses a `pgsql` scheme. The format is identical to [mysql](#mysql-dsn).

### Format

```bash
pgsql://user:password@host:port/dbname?charset=name&sslmode=mode&search_path=a,b,c
```

### Defaults

| value       | default                                                                                                                                                             |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| user        | postgres - ex: `pgsql://:password@...`                                                                                                                              |
| password    | empty                                                                                                                                                               |
| host        | 127.0.0.1 - ex: `pgsql:dbname`                                                                                                                                      |
| port        | 5432                                                                                                                                                                |
| dbname      | required value, no default                                                                                                                                          |
| charset     | UTF8                                                                                                                                                                |
| sslmode     | `verify-full` if other ssl options provided, otherwise `prefer`: see [PostgreSQL Parameter Key Words][sslmode]{:target="_blank"} for a list of all possible values. |
| search_path | set by postgres                                                                                                                                                     |

## GitHub DSN

The GitHub DSN uses a `github` scheme. You must use a [personal access token][github-pac]{:target="_blank"}
with at least repository read access.

{: .highlight }
It is recommended to use GitHub's new fine-grained personal access tokens. However, "classic" tokens will also work.

Optionally, you can enable repository write access. {{ page.git_write_benefits }}.

### Format

```bash
github:owner/repo/store_root?branch=branch&token=token
```

* authority `://` is not supported, GitHub always uses `api.github.com:443`
* `owner` is either GitHub username or organization <small>[required]</small>
* `repo` is the name of the GitHub repository <small>[required]</small>
* `store_root` is the path from the root of `repo` to the migration store root directory. If omitted, cinch uses
  the root of `repo` as the migration store root directory.
* `branch` option is the branch to use within `repo` <small>[required]</small>
* `token` option is the personal access token. If omitted, the `CINCH_GITHUB_TOKEN` environment variable must be set.

## GitLab DSN

The GitLab DSN uses a `gitlab` scheme. You must use a
[personal access token][gitlab-pac]{:target="_blank"}, [group access token][gitlab-gat]{:target="_blank"}, or
[project access token][gitlab-prat]{:target="_blank"} with at least `read_api, read_repository` scopes.

Optionally, you can enable `api` scope. {{ page.git_write_benefits }}. If `api` scope is
enabled, no other scopes are required.

### Format

```bash
# for gitlab.com (GitLab SaaS)
gitlab:project_id/store_root?branch=branch&token=token

# on premise (self-managed) - same as above if host is gitlab.com
gitlab://host:port/project_id/store_root?branch=branch&token=token
```

* `host` is required for self-managed GitLab. If omitted, `gitlab.com` is used
* `port` default is 443
* `project_id` is the GitLab project identifier, which can be found at the top of the project's main page <small>[required]</small>
* `store_root` is the path from the root of project to the migration store root directory. If omitted, cinch uses
  the root of project as the migration store root directory.
* `branch` option is the branch to use within project <small>[required]</small>
* `token` option is the personal, group, or project access token. If omitted, the `CINCH_GITLAB_TOKEN` environment variable must be set.

## Azure DevOps DSN

The Azure DevOps DSN uses a `azure` scheme. You must use a
[personal access token][azure-pac]{:target="_blank"} with at least `code read` access.

{: .warning }
Supports Azure DevOps Services (cloud), not Azure DevOps Server (on-premise).

Optionally, you can enable `code write` access. {{ page.git_write_benefits }}.

### Format

```bash
azure:organization/project/repo/store_root?branch=branch&token=token
```

* authority `://` is not supported, Azure always uses `dev.azure.com:443`
* `organization` is the Azure organization name <small>[required]</small>
* `project` is the name of the Azure project <small>[required]</small>
* `repo` is the name of the Azure repository <small>[required]</small>
* `store_root` is the path from the root of `repo` to the migration store root directory. If omitted, cinch uses
  the root of `repo` as the migration store root directory.
* `branch` option is the branch to use within `repo` <small>[required]</small>
* `token` option is the personal access token. If omitted, the `CINCH_AZURE_TOKEN` environment variable must be set.

[github-pac]: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token

[gitlab-pac]: https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html

[gitlab-gat]: https://docs.gitlab.com/ee/user/group/settings/group_access_tokens.html

[gitlab-prat]: https://docs.gitlab.com/ee/user/project/settings/project_access_tokens.html

[azure-pac]: https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=Windows

[sslmode]: https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-PARAMKEYWORDS

{% endcomment %}