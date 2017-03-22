# 🌲 Timber - Log Better. Solve Problems Faster.

[![ISC License](https://img.shields.io/badge/license-ISC-ff69b4.svg)](LICENSE.md)
[![Hex.pm](https://img.shields.io/hexpm/v/timber.svg?maxAge=18000=plastic)](https://hex.pm/packages/timber)
[![Documentation](https://img.shields.io/badge/hexdocs-latest-blue.svg)](https://hexdocs.pm/timber/index.html)
[![Build Status](https://travis-ci.org/timberio/timber-elixir.png?branch=master)](https://travis-ci.org/timberio/timber-elixir)

* [Timber website](https://timber.io)
* [Timber docs](https://timber.io/docs)
* [Library docs](https://hexdocs.pm/timber/readme.html)
* [Support](mailto:support@timber.io)

## Overview

Timber turns your raw text logs into rich JSON events that can be consumed by the
[Timber.io service](https://timber.io). It improves log data quality at the source, adding
critical event and context data to your logs so that you can filter out the noise and
solve problems faster.

For example, Timber turns this:

```
Sent 200 in 45.ms
```

Into this:

```
Sent 200 in 45.2ms @metadata {"dt": "2017-02-02T01:33:21.154345Z", "level": "info", "context": {"user": {"id": 1}, "http": {"method": "GET", "host": "timber.io", "path": "/path", "request_id": "abcd1234"}}, "event": {"http_response": {"status": 200, "time_ms": 45.2}}}
```

Allowing you to run queries like:

1. `context.request_id:abcd1234` - View all logs generated for a specific request.
2. `context.user.id:1` - View logs generated by a specific user.
3. `type:http_response` - View specific events (exceptions, sql queries, etc)
4. `http_server_response.time_ms:>=1000` - View slow responses with the ability to zoom out and view them in context (request, user, etc).
5. `level:error` - Levels in your logs!

## Installation

1. Add `timber` as a dependency in `mix.exs`:

    ```elixir
    # Mix.exs

    def application do
      [applications: [:timber]]
    end

    def deps do
      [{:timber, "~> 1.0"}]
    end
    ```

2. In your `shell`, run `mix deps.get`.

3. In your `shell`, run `mix timber.install`.


## Usage

<details><summary><strong>Basic logging</strong></summary><p>

No special API, Timber works directly with `Logger`:

```elixir
Logger.info("My log message")

# => My log message @metadata {"level": "info", "context": {...}}
```

---

</p></details>

<details><summary><strong>Custom events</strong></summary><p>

Custom events allow you to extend beyond events already defined in
the [`Timber.Events`](lib/timber/events) namespace.

```elixir
event_data = %{customer_id: "xiaus1934", amount: 1900, currency: "USD"}
Logger.info("Payment rejected", event: %{payment_rejected: event_data})

# => Payment rejected @metadata {"level": "warn", "event": {"payment_rejected": {"customer_id": "xiaus1934", "amount": 100, "reason": "Card expired"}}, "context": {...}}
```

* Notice the `:payment_rejected` root key. Timber will classify this event as such.
* In the [Timber console](https://app.timber.io) use the query: `type:payment_rejected` or `payment_rejected.amount:>100`.


---

</p></details>

<details><summary><strong>Custom contexts</strong></summary><p>

Context is additional data shared across log lines. Think of it like log join data.
Custom contexts allow you to extend beyond contexts already defined in
the [`Timber.Contexts`](lib/timber/contexts) namespace.

```elixir
Timber.add_context(%{build: %{version: "1.0.0"}})
Logger.info("My log message")

# => My log message @metadata {"level": "info", "context": {"build": {"version": "1.0.0"}}}
```

* Notice the `:build` root key. Timber will classify this context as such.
* In the [Timber console](https://app.timber.io) use the query: `build.version:1.0.0`

</p></details>


## Jibber-Jabber

<details><summary><strong>Which log events does Timber structure for me?</strong></summary><p>

Out of the box you get everything in the [`Timber.Events`](lib/timber/events) namespace.

We also add context to every log, everything in the [`Timber.Contexts`](lib/timber/contexts)
namespace. Context is structured data representing the current environment when the log line
was written. It is included in every log line. Think of it like join data for your logs.

---

</p></details>

<details><summary><strong>What about my current log statements?</strong></summary><p>

They'll continue to work as expected. Timber adheres strictly to the default `Logger` interface
and will never deviate in *any* way.

In fact, traditional log statements for non-meaningful events, debug statements, etc, are
encouraged. In cases where the data is meaningful, consider [logging a custom event](#usage).

</p></details>

<details><summary><strong>How is Timber different?</strong></summary><p>

1. **It's just _better_ logging**. Nothing beats well structured raw data. And that's exactly
   what Timber aims to provide. There are no agents, special APIs, or proprietary data
   sets that you can't access.
2. **Improved log data quality.** Instead of relying on parsing alone, Timber ships libraries that
   structure and augment your logs from _within_ your application. Improving your log data at the
   source.
3. **Human readability.** Timber _augments_ your logs without sacrificing human readability. For
   example: `log message @metadata {...}`. And when you view your logs in the
   [Timber console](https://app.timber.io), you'll see the human friendly messages
   with the ability to view the associated metadata.
4. **Long retention**. Logging is notoriously expensive with low retention. Timber
   offers _6 months_ of retention by default with sane prices.
5. **Normalized schema.** Have multiple apps? All of Timber's libraries adhere to our
   [JSON schema](https://github.com/timberio/log-event-json-schema). This means queries, alerts,
   and graphs for your ruby app can also be applied to your elixir app (for example).

---

</p></details>

---

<p align="center" style="background: #221f40;">
<a href="http://github.com/timberio/timber-elixir"><img src="http://files.timber.io/images/ruby-library-readme-log-truth.png" height="947" /></a>
</p>