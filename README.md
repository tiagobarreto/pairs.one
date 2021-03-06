# pairs.one

This is the source code for http://pairs.one - a neat multiplayer online memory/concentration game.

To start the app:

  * Install dependencies with `mix deps.get`
  * Install Node.js dependencies with `npm install`
  * With Redis installed, start it with `redis-server`
  * Start Phoenix endpoint with `mix phoenix.server`

Now you can visit [`localhost:4000`](http://localhost:4000) from your browser.

Below is a brief high-level overview of how things are put together.

## Multiplayer features

1 to 5 players may simultaneously take part in the game. The first user always creates the game, then sends out the link
to the opponents. The first n-1 users to visit the link become the players, the rest will be able to join the game in
the "watch-only" mode.

## Game state updates

On the client, the dynamic game state is handled by the `Types.Game` type (`web/static/elm/Types/Game.elm`). It
includes the state of the board, the current turn, the players' names etc. On every change of this data, the _complete
state_ gets broadcast to all other players in JSON format. To minimize the payload, it gets compressed with lz-string,
then encoded with base64. Compression/decompression and base64 encoding on the client gets executed via Elm ports.

## State persistence

On the server, the game state gets stored in Redis, each game identified with the `game:<gameid>` key, where `gameid` is a random string
that gets generated at game creation and becomes the last part of the game's URL. The game gets removed from Redis after
24 hours of inactivity.

## Known issues

This game won't work in IE, as well as on the networks where websocket traffic is blocked.

## Tests

There are no tests yet.

## Translating game into other languages

If you like playing this game and want to help translate it into your language, please, submit a PR with the following modifications (on an example of French):

* Create a new gettext locale with `mix gettext.merge priv/gettext --locale=fr`

* Modify `/priv/gettext/fr/LC_MESSAGES/default.po`

* Add `web/static/elm/I18n/Fr.elm` (see `web/static/elm/I18n/En.elm` for reference)

* Copy `web/templates/page/index.en.html.eex` to `web/templates/page/index.fr.html.eex` and translate it (also, update
    the link to "Create game" button).

* Add `fr` to the list of available locales in `config/config.exs` and create a new label with `French` in `web/views/layout_view.ex`

Fire up the app and make sure your translation works.

---
Copyright (c) 2016 Good Bit Labs Limited, released under MIT
