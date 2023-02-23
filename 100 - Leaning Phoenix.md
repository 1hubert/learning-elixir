- Official website: https://phoenixframework.org/
- Phoenix is a web development framework written in Elixir.
- Phoenix implements the server-side Model View Controller (MVC) pattern.
- Offers high developer productivity AND high application performance.

- Promotes the usage of git as a version control software: By default generates `.gitignore` in a new project. We can `git init` our repository and immediately add and commit all that hasn't been marked ignored.
- When running in dev mode (`MIX_ENV=dev`), Phoenix watches for any changes in `assets` and takes care of updating your front-end application as you work.

- https://grox.io/
- https://pragmaticstudio.com/

## Solutions to common issues
- `The database for <project>.Repo couldn't be created`
	- Make sure postgresql is installed 
	- Run `net start postgresql-x64-14` in cmd (admin)
- Cannot create a new project with mix:
	- Install/upgrade hex: `$ mix local.hex`
- Phoenix app wants to `ecto.migrate` for some unknown reason, then it can't even create the table because it already exists
	- Run mix `ecto.migrations` This will show you the migrations you have. This list will (hopefully) match the list in `/priv/repo/migrations`. Go to `_build/<your_app_name>/priv/repo/migrations` and delete the files from here that are not in the other two migration lists.
- `lib/discuss_web/templates/topic/new.html.heex:1: undefined function topic_path/2`
	- Make sure to use `Routes.<controller_name>_path` instead of just `<controller_name>_path`. Phoenix changed from importing route helpers to aliasing the module quite a while ago.
- `Repo.insert(changeset)` not defined
	- specify the full name of your repo, for example: `Discuss.Repo.insert(changeset)`
- column `inserted_at` does not exist
	- You've probably used `timestamps()` in your schema (which is in your context?), but haven't used `timestamps()` in your migration which made for a mismatch between how you told Phoenix to communicate with your database (Phoenix listen, there will be `inserted_at` and `updated_at` fields) and what fields your database actually has
- `(Mix.Error) Can't continue due to errors on dependencies`
	1) Set elixirLS.fetchDeps to false.
	2) Close VSCode just to be safe.
	3) rm -r deps _build .elixir_ls && mix deps.get.
	4) Start VSCode.
	5) You should only have to do this once.
- You're trying to access a route but it's behaving in a weird way. ex. `function DiscussWeb.AuthController.request/2 is undefined or private`
	- Make sure that the non-wildcard routes in `router.ex` are above the wildcard ones
![[Pasted image 20221220235357.png]]
- `build_asoc` not defined
	- Use `Ecto.build_asoc`
- Some weird problems with `@derive {Poison.Encoder, only: [:content]}`
	- Use `@derive {Jason.Encoder, only: [:content]}`
- `** (Postgrex.Error) ERROR 42P07 (duplicate_table) relation "employees" already exists`
	1) Open psql
	2) `\list` or `\l` to list all databases
	3) You can execute SQL: so use `drop database <database>` on both dev and test dbs
- Need to go back to last commit in git (drop all changes)
	1) `git reset --hard`
	2) `git clean -fd`
- Updated `.gitignore` but `git status` still tracks it
	- An important question is: should the file remain in the repository or not? Eg if someone new clones the repo, should they get the file or not? If YES then `git update-index --assume-unchanged <file>` is correct and the file will remain in the repository and changes will not be added with git add. If NO (for example it was some cache file, generated file etc), then `git rm --cached <file>` will remove it from repository. 

## Extensions for VSCode
- ElixirLs
- Elixir Mix Formatter
- Elixir Templates Formatter
- Phoenix Framework

## PSQL
- `\c <database>` to connect
- `\dt` to list all tables in the current schema

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
- If the above is not working make sure you have archive installed: 
`mix archive.install hex phx_new`

## Ecto
- Phoenix uses another Elixir package called `Ecto` to talk to actual databases like PostgreSQL (default), MySQL, MSSQL, SQLite3.
- `mix ecto.create` to create a new database

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
- `mix phx.routes` to list routes
- `get "/", PageController, :index` explained:
	1) `get` means an HTTP GET request
	2) `"/"` is the route
	3) `PageController` is a controller. A controler is a module in a specified file in the `controllers` folder. This controller is a `<project_name>Web.PageController` module in `controllers/page_controller.ex` file.
	4) `:index` is an action. An action is just a named function in the specified controller (in this case `PageController`)
- Basic functions to use inside your actions:
	1) `render(conn, "index.html)"` - Renders an HTML page
	2) `text(conn, "text goes here")` - Shows user a blank page with some text
	3) `redirect(conn, external: "https://google.com")` - Redirects to another website
	4) `redirect(conn, to: Routes.page_path(conn, :index))` - Redirects to `index` action

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

## Phoenix itself is a pipeline
```
conn
|> endpoint
|> router
|> controller
|> view
```

## MVC
1) A request is made to show pictures of cats
2) The **controller** handles the request and forwards it to the **model** which makes a database query returning cat images
3) The images go through the **controller** which sends them to the **view**
4) The user-friendly view goes back through the **controller** as a response to the original request

## Parameters
- If you want to pass extra parameters use the following syntax:
`localhost:4000/subpage?parameter1=value1&parameter2=value2&parameter3=value3`
Then, within your controller's matching action the value of `params` argument will be a map:
`params = %{"parameter1" => "value1", "parameter2" => "value2", "parameter3" => "value3"}`

## Migrations
- `priv/repo/migrations`
- The naming convention is `<uniqueid>_<name>_migration_table.exs`. The unique id can be the current date.
- The naming convention for the module is `<projectname>.Repo.Migrations.<migrationname>`. First line within that module should be `Ecto.Migration`
- Example migration:
```
defmodule PhoenixFundamentals.Repo.Migrations.CreateUsers do
  use Ecto.Migration

  def change do
    create table(:users) do
      add :name, :string
      add :email, :string

      timestamps()
    end
  end
end
```
- Run the migration with `mix ecto.migrate`

## Changeset
1) After doing the migration above, let's create a `user.ex` file in the same folder as `repo.ex`
```
defmodule PhoenixFundamentals.User do
  use Ecto.Schema
  import Ecto.Changeset

  alias PhoenixFundamentals.User

  schema "users" do
    field :name, :string
    field :email, :string

    timestamps()
  end

  def changeset(%User{} = user, attrs) do
    user
    |> cast(attrs, [:name, :email])
    |> validate_required([:name, :email])
  end
end
```
2) Then, in the interactive shell we can do different operations on our users table
```
iex> alias PhoenixFundamentals.{Repo, User}
iex> Repo.insert(%User{name: "John"})

INSERT INTO "users" ("name","inserted_at","updated_at") VALUES ($1,$2,$3) RETURNING "id" ["John", ~N[2022-12-12 20:14:42], ~N[2022-12-12 20:14:42]]
{:ok,
 %PhoenixFundamentals.User{
   __meta__: #Ecto.Schema.Metadata<:loaded, "users">,
   id: 1,
   name: "John",
   email: nil,
   inserted_at: ~N[2022-12-12 20:14:42],
   updated_at: ~N[2022-12-12 20:14:42]
 }}
```
- `Repo.insert(%User{name: "John", email: "example@gmail.com"}` to add a new user
- `Repo.all(User)` to get all records
3) Modifyng an existing record: 
```
iex> old_user = Repo.get!(User, 1)
iex> replacement_user = User.changeset(old_user, %{name: "John Updated", email: "new@gmail.com"})
iex> Repo.update(replacement_user)
```
4) Delete an existing user
```
iex> u = Repo.get!(User, 1)
iex> Repo.delete(u)
```

## Phx.gen
https://hexdocs.pm/phoenix/contexts.html
Generates a controller, view and context for our HTML resource that's going to be our task manager.
- `mix phx.gen.html Tasks Task tasks text:string completed:boolean` explained
1) `mix phx.gen.html` - generates a  resource
2) `Tasks` - call the context
3) `Task` - call the schema
4) `tasks` - table in our database
5) `text: string completed:boolean` - fields

## Migrations
- Migrations are files that are used to modify the database schema.

## Authentication and Authorisation
- Authentication is where you prove who you are and authorisation is where the application decides what you can do.

## Some lingo
- Single Table Inheritance - treating tables in a relational db similarly to classes in OOP (don't do that...)
- MFA - Module.Function/Arity
- GenServer - stands for General Server
- PID - stands for Process ID

## Ecto associations
- In an example table "bookmarks" a "user_id" representing an id of the user who made a request to bookmark something is what we call a "foreign key".

## `.iex.exs`
- If you often find yourself aliasing the same thing in the  interactive shell, just put all the imports/aliases etc in this file in your main directory.

## Repo
- From default the comments won't load, you need to use:
`Repo.all(Post) |> Repo.preload(:comments)`

## Keyword lists
- Often used as parameters (params) for a function. Example:
	- `Repo.get_by(User, [username: "alice"]`
	- But we actually can omit the square brackets and it still will be a valid keyword list
	- `Repo.get_by(User, username: "alice")`
	- In the above example, a map instead of a keyword list would also work, but the convention is to to use a non-bracketed keyword list.

## Ecto.Multi
- The output of `Repo.transaction()` is quite heavy, so usually you would handle it at the end of the function like this:
```
def reset_employee_password(employee, attrs) do
    Ecto.Multi.new()
    |> Ecto.Multi.update(:employee, Employee.password_changeset(employee, attrs))
    |> Ecto.Multi.delete_all(:tokens, EmployeeToken.employee_and_contexts_query(employee, :all))
    |> Repo.transaction()
    |> case do
      {:ok, %{employee: employee}} -> {:ok, employee}
      {:error, :employee, changeset, _} -> {:error, changeset}
    end
  end
```
