This is a fork of [the original
`matterbridge-heroku`](https://github.com/cadecairos/matterbridge-heroku).

It includes [a custom config file][config] specific to one
implementation, but this can easily be modified in a new fork.

Currently, deploying this will make a bridge between g0v-tw.slack.com
and g0v.zulipchat.com. Secrets are handled in Heroku.

# Setup instructions for this fork

1. Check that the right bridges are configured in
   `config/config-heroku-template.toml`. See [this
   doc](https://github.com/42wim/matterbridge#configuration) for what
   goes in that file.
2. Set up a Slack App (Classic) and bot, get the "Bot User OAuth
   Token", and subscribe the bot to the relevant channels, using
   [these
   instructions](https://github.com/42wim/matterbridge/wiki/Slack-bot-setup#bot-based-setup).
   (Ignore the "Legacy setup" section.)
3. Set up a Zulip bot, get its email and API key, and subscribe the
   bot to the relevant streams, using [these
   instructions](https://github.com/42wim/matterbridge/wiki/Section-Zulip-%28basic%29).
4. Deploy to Heroku.
   1. Click the "Deploy to Heroku" button below.
   2. Choose an app name.
   3. Fill in the secrets you obtained above.
   4. When the Heroku instance is set up, go to the "Resources" tab
      and switch on the worker called `./start.sh`. You'll need to
      click the edit icon on the item before the switch will respond.
   5. Redeploy after making the change in the previous step. Heroku
      has a few ways to do deployments, under the "Deploy" tab. I've
      tested with Heroku's GitHub integration, which allows automatic
      deploys when the repository's main branch is changed.

# Matterbridge-Heroku

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

An inline buildpack for hosting Matterbridge on Heroku.

[**Heroku**][heroku] is a platform for easily deploying applications.

A [**buildpack**][buildpacks] provides framework and runtime support for
apps running on platforms like Heroku.

An [**_inline_ buildpack**][inline-buildpacks] is a special buildpack
that includes code for both the app and the buildpack that _runs_ the
app.

[**Matterbridge**][matterbridge] is a simple _bridge_ that can relay
messages between a number of different chat services, essentially
connecting separate chat tools.

   [heroku]: https://www.heroku.com/what
   [buildpacks]: https://docs.cloudfoundry.org/buildpacks/
   [inline-buildpacks]: https://github.com/kr/heroku-buildpack-inline#readme
   [matterbridge]: https://github.com/42wim/matterbridge#readme

This repo includes [a custom config file][config] specific to a sample
implementation, but this is intended to be modified in your own fork.

## Configuration

Configuration happens via environment variables and a configuration
template file.

### Environment: Buildpack

- `MATTERBRIDGE_VERSION` Required. Use a [matterbridge git tag][git-tags].
- `MATTERBRIDGE_URL` Optional. Use this to download the binary from a
  custom url instead of the tagged release from the official GitHub
repo.  (Setting this makes `MATTERBRIDGE_VERSION` ignored.)
    * With caution, you may want to use the [latest nightly matterbridge
      build](https://bintray.com/42wim/nightly/Matterbridge/_latestVersion)
      while waiting on the next official release.

### Environment: Matterbridge

Matterbridge has some configuration of its own, which is mostly
documented upstream. For starters, we'll review the most important
aspects:

* `DEBUG`. Set to "1" to log all message events across bridges.

Matterbridge uses Viper, and so each value in the TOML configuration can
be set by envvar.

Basically, here are the rules:

- Each config envvar is prefixed with `MATTERBRIDGE_`.
- Each nested level of config object is separated by an underscore `_`.
- Any dash in a config key is converted to an underscore `_`.

So for example, with this in your TOML config:

```toml
[slack.my-team]
Token="xoxp-xxxxxxxxxxxxxxxxxxxxxxxxxxx"
```

You could instead set an environment variable for
`MATTERBRIDGE_SLACK_MY_TEAM_TOKEN` and leave that key out of in the
configuration file template.

### Template: Matterbridge

* Edit channel bridge config via [`config/config-heroku-template.toml`][config].

<!-- Links -->
   [git-tags]: https://github.com/42wim/matterbridge/tags
   [config]: config/config-heroku-template.toml
