# Multiverse

[![Deps Status](https://beta.hexfaktor.org/badge/all/github/Nebo15/multiverse.svg)](https://beta.hexfaktor.org/github/Nebo15/multiverse) [![Hex.pm Downloads](https://img.shields.io/hexpm/dw/multiverse.svg?maxAge=2592000)](https://hex.pm/packages/multiverse) [![Latest Version](https://img.shields.io/hexpm/v/multiverse.svg?maxAge=2592000)](https://hex.pm/packages/multiverse) [![License](https://img.shields.io/hexpm/l/multiverse.svg?maxAge=2592000)](https://hex.pm/packages/multiverse) [![Build Status](https://travis-ci.org/Nebo15/multiverse.svg?branch=master)](https://travis-ci.org/Nebo15/multiverse) [![Coverage Status](https://coveralls.io/repos/github/Nebo15/multiverse/badge.svg?branch=master)](https://coveralls.io/github/Nebo15/multiverse?branch=master)

This plug helps to manage multiple API versions based on request and response gateways

Inspired by Stripe API.

## Installation

The package (take look at [hex.pm](https://hex.pm/packages/multiverse)) can be installed as:

  1. Add `multiverse` to your list of dependencies in `mix.exs`:

    ```elixir
    def deps do
      [{:multiverse, "~> 0.1.0"}]
    end
    ```

  2. Ensure `multiverse` is started before your application:

    ```elixir
    def application do
      [applications: [:multiverse]]
    end
    ```

## How to use

Insert this plug into your API pipeline (```router.ex```):

```
pipeline :api do
  plug :accepts, ["json"]
  plug :put_secure_browser_headers
  plug Multiverse
end
```

Create your first API gateway

```
defmodule GateName do
  @behaviour MultiverseGate

  def mutate_request(%Plug.Conn{} = conn) do
    # Mutate your request here
    IO.inspect "GateName.mutate_request applied to request"
    conn
  end

  def mutate_response(%Plug.Conn{} = conn) do
    # Mutate your response here
    IO.inspect "GateName.mutate_response applied to response"
    conn
  end
end
```

Attach gate to multiverse:

```
pipeline :api do
  plug :accepts, ["json"]
  plug :put_secure_browser_headers
  plug Multiverse, gates: [
    "2016-07-31": GateName
  ]
end
```

***Notice:*** your API versions should be strings in YYYY-MM-DD format to be appropriately compared to current version.

Send your API requests with ```X-API-Version``` header with version lower than ```2016-07-31```.

## Custom version header

You can use any version headers by passing option to Multiverse:

```
pipeline :api do
  plug :accepts, ["json"]
  plug :put_secure_browser_headers
  plug Multiverse, gates: [
    "2016-07-31": GateName
  ], version_header: "X-My-API-Version"
end
```

## Custom error handlers

Sometimes clients are sending corrupted version headers, by default Multiverse will fallback to "latest" version. But you can set your own handler for this situations:

```
pipeline :api do
  plug :accepts, ["json"]
  plug :put_secure_browser_headers
  plug Multiverse, gates: [
    "2016-07-31": GateName
  ], error_callback: {ModuleName, :module_method}
end
```

## Structuring your tests

TODO: give recommendations on tests.

## Links

- [MOVE FAST, DON'T BREAK YOUR API](http://amberonrails.com/move-fast-dont-break-your-api/)
