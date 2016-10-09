# Polygot

A library to serve your Phoenix app in differnt locales.

## Installation

The package can be installed as:

Add `polygot` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [{:polygot, "~> 0.1.0"}]
end
```

Ensure `polygot` is configured in `config/config.exs`:

```elixir
config :polygot,
    locales: %{
        "en-GB" => %{ path_prefix: "gb" },
        "en-US" => %{ path_prefix: "us" }
    },
    gettext_module: YourApp.Gettext,
    gettext_domain: "routes"
```

* `locales` this is a map of the locales you want to support, the key will also be used as the name for your Gettext locale.
    * `path_prefix` is the prefix that will be used in your urls, for example: `http://example.com/gb` will load the `en-GB` locale.
* `gettext_domain` is the domain to use for your route translations, in this case it will fetch the strings from `routes.po`
* `gettext_module` is the Gettext module to use, it will most probably be `{YourAppModule}.Gettext`

## Router

You'll need to import `Polygot` to your router(s), it is recommended that you do so in the `def router do` section in `web/web.ex`:

```elixir
import Polygot
```

this will let you be able to use the `localize` macro in your routes like this:

```elixr
localize get "/", PageController, :index
```

if we run `mix phoenix.routes` we'll see that it created all the routes for our defined locales:

```zsh
$ mix phoenix.routes
page_path  GET  /gb  PolygotExample.PageController [action: :index, locale: "en-GB"]
page_path  GET  /us  PolygotExample.PageController [action: :index, locale: "en-US"]
```

Now all that's left to do is to add Polygot's plug into your pipeline, so that it can set the appropiate locale based on the requested path:

```elixir
plug Polygot.Plug
```

Now when you load `http://exmple.com/gb` the `:locale` assign will be equal to `:en-GB`, and your Gettext locale will be set to `en-GB` automatically.

## Route helpers

To generate localized routes we'll need to add this:

```elixir
import Polygot.Helpers
```

to our `controller` and `view` sections of our `web/web.ex`

now similarly to the routing, we can use `localize` to generate translated paths and urls:

```elixir
localize page_path(conn, :index)
```
