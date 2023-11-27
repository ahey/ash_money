# Getting Started With AshMoney

The primary thing that AshMoney provides is `AshMoney.Types.Money`. This is backed by `ex_money`. You can use it out of the box like so:

```elixir
attribute :balance, AshMoney.Types.Money
```

Or you can add it to your compile time list of types for easier reference:

```elixir
config :ash, :custom_types, money: AshMoney.Types.Money
```

Then compile ash again, `mix deps.compile ash --force`, and refer to it like so:

```elixir
attribute :balance, :money
```

## AshPostgres Support

Thanks to `ex_money_sql`, there are excellent tools for lowering support for money into your postgres database. This allows for things like aggregates that sum amounts, and referencing money in expressions:

```elixir
sum :sum_of_usd_balances, :accounts, :balance do
  filter expr(fragment("(?).currency_code", balance).currency_code == "USD")
end
```

To install it, add `AshMoney.AshPostgresExtension` to your list of `installed_extensions` in your repo, and generate migrations.

```elixir
defmodule YourRepo do
  def installed_extensions do
    [..., AshMoney.AshPostgresExtension]
  end
end
```

## AshGraphql Support

Add the following to your schema file:

```elixir
  object :money do
    field(:amount, non_null(:decimal))
    field(:currency, non_null(:string))
  end
```