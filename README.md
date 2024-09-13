# openvsx

Minimal docker-compose config to set up your own [OpenVSX](https://github.com/eclipse/openvsx). Based on this [issue](https://github.com/eclipse/openvsx/issues/703).

[Open VSX](https://github.com/eclipse/openvsx) is a [vendor-neutral](https://projects.eclipse.org/projects/ecd.openvsx) open-source alternative to the [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode). It provides a server application that manages [VS Code extensions](https://code.visualstudio.com/api) in a database, a web application similar to the VS Code Marketplace, and a command-line tool for publishing extensions similar to [vsce](https://code.visualstudio.com/api/working-with-extensions/publishing-extension#vsce).

## Requirements

Having node > 18

Install `ovsx` cli:

```
npm install --global ovsx
```

> This CLI will allows us to public VSIX extension.

## Setup

Run:

```
docker-compose up -d
``` 

Create a user that will be used to publish `.vsix`:

```
docker exec -it <postgres_container_id> psql -U openvsx -d openvsx -c "INSERT INTO user_data(id, login_name, role) VALUES(1001, 'super_user', 'admin');"
docker exec -it <postgres_container_id> psql -U openvsx -d openvsx -c "INSERT INTO personal_access_token(id, active, description, value, user_data) VALUES(1002, TRUE, 'For publishing extensions', 'super_token', 1001);"
```

## Publishing a VSIX extension

Example: for this [extension](https://open-vsx.org/extension/jeanp413/open-remote-ssh)

```
ovsx -r http://172.17.0.1:8080 -p super_token create-namespace jeanp413
ovsx -r http://172.17.0.1:8080 -p super_token publish jeanp413.open-remote-ssh-0.0.46.vsix
```