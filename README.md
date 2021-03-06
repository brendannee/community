# community.hackerschool.com

![Travis Build Status](https://travis-ci.org/hackerschool/community.svg?branch=master)

## Dependencies

- Ruby 2.1.2
  - bundler
  - foreman
- Postgres 9.3.4
- redis
- leiningen

### Installing dependencies on OS X using rvm, Homebrew, and Postgres.app

We recommend [Postgres.app](http://postgresapp.com/) if you're on OS X.

**Ruby stuff using rvm:**

```sh
$ rvm get head
$ rvm install ruby-2.1.2
$ rvm use ruby-2.1.2
$ gem install bundler
$ gem install foreman
```

**Leiningen and redis using Homebrew:**

```sh
$ brew update
$ brew install leiningen
$ brew install redis
# follow the printed instructions to have redis start automatically on boot
```

## Configuration

We use foreman which sets environment variables from a `.env` file. We don't
check this into version control because it contains secret information. Here is
a sample `.env` file to get you started:

```sh
PORT=5001
RACK_ENV=development
DATABASE_URL=postgres://localhost/community_development
REDIS_URL=redis://localhost:6379

HACKER_SCHOOL_CLIENT_ID=your_client_id
HACKER_SCHOOL_CLIENT_SECRET=your_client_secret

JASMINE_CONFIG_PATH=test/javascripts/support/jasmine.yml

# If you want to develop against a local copy of the Hacker School API, add:
HACKER_SCHOOL_SITE=http://localhost:5000

# If you want to import accounts from Hacker School
# (You won't be able to do this unless you are Hacker School faculty)
HACKER_SCHOOL_API_SECRET_TOKEN=hacker_school_api_secret

# Needed for batch mail sending in production
MAILGUN_API_KEY=your_mailgun_api_key
```

To generate a Hacker School client id and secret, go to your [Hacker School settings page](https://www.hackerschool.com/settings) and make a new OAuth app. The redirect url should be `http://localhost:5001/login/complete` (or your development host name if you don't develop off of localhost).

## Running the code

The first time you run the code, install necessary gems and set up your database.

```sh
$ bundle
$ bin/rake db:setup
```

After that, start your development server and start building the client JS.

```sh
$ foreman start

# In another terminal:
$ cd client
$ lein cljsbuild auto
```

### Running the production ClojureScript

The production client code will sometimes function differently than the development code. This can happen when you forget to add externs for a library you are calling, or if `lein cljsbuild auto` randomly makes a bad client. Because of this, we should test the production client before deploying. To do that, you can set `CLIENT_ENV` to production and run Foreman. If you don't set CLIENT_ENV, it defaults to the Rails environment.

```sh
$ CLIENT_ENV=production foreman start
```

## Where is `db/schema.rb`?

Look at `db/structure.sql` instead. We use this because it supports Postgres views.

## Client testing

We use a small ClojureScript wrapper over [Jasmine](http://jasmine.github.io/2.0/introduction.html) for testing our client. If you're running `lein cljsbuild auto` (as above), the client tests will be built automatically.

To run the client tests:

```sh
$ bin/rake jasmine
# Navigate to localhost:8888 to run the tests.
# Refresh the page to re-run the tests.
```

## Getting a ClojureScript REPL up and running

Community supports a ClojureScript REPL so long as you're running the development build. To connect, start an nREPL server and connect to it using your preferred client. If you're using Emacs, that probably means [cider](https://github.com/clojure-emacs/cider). If you're using Vim, that probably means [vim-fireplace](https://github.com/tpope/vim-fireplace). Otherwise, `cd` into `client` and run `lein repl`.

Once in the REPL:

```clj
user=> (browser-repl)
<< started Weasel server on ws://127.0.0.1:9001 >>
Type `:cljs/quit` to stop the ClojureScript REPL
nil
cljs.user=>
```

Open your dev site and run the following in the JS console:

```js
community.dev.repl_connect()
// if successful, you'll see "Opened Websocket REPL connection" printed
```

Back in your ClojureScript REPL:

```clj
cljs.user=> (js/alert "hello, browser!")
```

# License

Copyright © 2014 Hacker School

This software is licensed under the terms of the AGPL, Version 3. The complete license can be found at http://www.gnu.org/licenses/agpl-3.0.html.
