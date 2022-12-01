- Official website: https://phoenixframework.org/
- Phoenix is a web development framework written in Elixir.
- Phoenix implements the server-side Model View Controller (MVC) pattern.
- Offers high developer productivity AND high application performance.

- Promotes the usage of git as a version control software: By default generates `.gitignore` in a new project. We can `git init` our repository and immediately add and commit all that hasn't been marked ignored.
- When running in dev mode (`MIX_ENV=dev`), Phoenix watches for any changes in `assets` and takes care of updating your front-end application as you work.

- https://grox.io/
- https://pragmaticstudio.com/

## The LiveView Life Cycle
1) Router
2) Initial Mount
	1) Mount (HTML and Websocket)
	2) `handle_params()`
3) Websocket Mount
	1) Mount (HTML and Websocket)
	2) `handle_params()`
4) Responding to Events
	1) `handle_event()`
	2) `render()`

## LiveView Layers
1) Endpoint (turns an HTTPS request into Elixir code)
2) Router - decides what LiveView to send the request
	1) Security responsibilities
	2) Authentication responsibilities
	3) Passes your requests to the correct plug
3) Requests lands on a live view 
	1) HTML mount, Mount function ->  handle_params -> render
	2) Websocket connection is made, mount function -> handle_params -> render
4) User started editing a form on the live view
	1) handle_event -> usually interacts with the database
	2) Very often we leave the web stack and enter the pure Elixir stack
5) Content Layer
	1) Responsible for business logic
	2) Responsible for saving data to the database
6) Schema layer
	1) Responsible for data validation
	2) Database logic

## Dependencies in a Phoenix app
- The Erlang VM and the Elixir programming language
- A database - Phoenix recommends PostgreSQL, but you can pick others or not use a database at all
- And other optional packages.

## Hex
- It's a package manager like `pip` in Python
- Install/upgrade hex: `$ mix local.hex`

## Create a new Phoenix app
- `mix phx.new` (or `mix help phx.new` to get help on the command)

## Communicating with databases
- Phoenix uses another Elixir package called `Ecto` to talk to actual databases like PostgreSQL (default), MySQL, MSSQL, SQLite3.

## PostgreSQL
- Use psql tool for basic stuff like managing roles, chaning passwords

## Project structure
https://hexdocs.pm/phoenix/directory_structure.html
- `_build` - holds compilation artifacts. Excluded from version control by default. Deleting this directory will force mix to rebuild your app.
- `assets` - front-end assets: JavaScript, CSS. Automatically managed by the `esbuild` tool
- `config` - all project configuration. `config/runtime.exs` is the best place to read secrets and other dynamic configuration.
- `deps` - all mix dependencies. You can find dependencies listed in the `mix.exs` file inside the `deps` function. Excluded from version control. Removing it will force mix to redownload.
- `lib` - that's where you do most of the work :). Holds application source code. Broken into `lib/<project>` (hosts business logic) and `lib/<project>_web` (web-related).
- `priv` - resorces necessary in production, but not directly part of your source code. Typically: database scripts, translation files.
- `test` - tests usually mirroring structure of `lib`.

## CSS
- Phoenix ships with a handful of custom styles in `assets/css/phoenix.css`, providing minimal setup for new projects.
- Phoenix ships with Milligram CSS Framework.
- You may move to any CSS framework of your choice.

## Router
- The router maps unique HTTP verb/path pairs to controller/action pairs which will handle them. Controllers in Phoenix are simply Elixir modules. Actions are functions that are defined within these controllers.

## Basics of creating new subpages
- https://hexdocs.pm/phoenix/request_lifecycle.html
### Routes
### Controllers
### Views
### Templates
- `.heex` files

## .heex
- Stands for "HTML + EEx"
- EEx is a library for embedding Elixir that ships as part of Elixir itself.
- `<% elixir_code_inside %>`
- `<%= return_val_will_be_shown %>`
- `@messenger` = `assign.messenger`

## Layouts
- Layouts give you the power to turn a simple `.heex` file to nice-looking full-blown `.html` page.
- Inside of `lib/<project>_web/templates/layout` there's a file `app.html.heex`. There's a line inside that looks like:
```
<%= @inner_content %>
```
- That line injects our template from `lib/<project>_web/templates/<endpoint_name>` inside of that layout. Example template:
```
<section class="phx-hero">
  <h2>Hello World, from Phoenix!</h2>
</section>
```

## The way of an HTTP request
1) Starts in our application endpoint (For example: `HelloWeb.Endpoint` in `lib/hello_web/endpoint.ex`)
2) Endpoint (the Elixir module) calls to `plug`. Plug is an essential library for handing requests.
3) Last plug is precisely the router module (for example: `HelloWeb.Router`).
4) Router module maps verb/path (ex. GET "/about") to specified controller.
5) The controller tells a view to render a template.

## Layers of an request
- endpoint (`Phoenix.Endpoint`)
- router (`Phoenix.Router`) - allows to scope functionality for different groups of users
- controller (`Phoenix.Controller`) - prepares data for the presentation layer
- view (`Phoenix.View`) - The presentation layer. Takes structured data from the controller and visualizes it for user.

## New page with POST functionality
- https://hexdocs.pm/phoenix/request_lifecycle.html#another-new-route

## New route
- `:<parameter>` (ex. `:messenger`) syntax in the path means that Phoenix will take whatever value appears in that position in the URL and convert it into a parameter. Example: If we point the browser to `http://localhost:4000/hello/Ocean`, the value of `"messenger"` will be `"Ocean"`.

## Function plugs
https://hexdocs.pm/phoenix/plug.html#function-plugs
- 2 positional arguments:
	- connection struct (`%Plug.Conn{}`)
	- connection options
- Returns a connection struct

## Module plugs
https://hexdocs.pm/phoenix/plug.html#module-plugs