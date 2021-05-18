This is a fork of [the original
`matterbridge-heroku`](https://github.com/cadecairos/matterbridge-heroku).

It includes [a custom config file][config] specific to one
implementation, but this can easily be modified in a new fork.

Currently, it is set up to bridge between g0v-tw.slack.com and
g0v.zulipchat.com. Secrets are handled in Heroku.

# Setup instructions for this fork

1. Check that the right bridges are configured in
   `config/config-heroku-template.toml`. See [this
   doc](https://github.com/42wim/matterbridge#configuration) for what
   goes in that file.
2. Set up Slack. You can follow [these
   instructions](https://github.com/42wim/matterbridge/wiki/Slack-bot-setup#bot-based-setup),
   with a few changes:
   - Under "Create the classic Slack App":
     - In step 3, which starts with "Navigate to the `Bot Users`
       page", instead click "Where's Bot User". A modal will appear,
       saying "You can find the bot user feature in a new place, App
       Home." Follow the link to App Home, and click "Add Legacy Bot
       User" from there.
   - Under "Grant scopes and install the Slack App":
     - In step 1, which starts with "Via the menu on the left side of
       the page", make sure you press the "Add an OAuth Scope" button
       instead of the much more prominent "Update Scopes" button.
     - In step 4, take note of the `Bot User OAuth Access Token`;
       you'll need that to set a Heroku environment variable later.
   - Under "Invite the bot to channels synced with Matterbridge":
     - Don't forget make sure that the channels you want to bridge (1)
       exist, and (2) are subscribed to by the new bot; otherwise,
       things will break. The channels to be bridged are specified in
       config/config-heroku-template.toml. (Each `[[gateway.inout]]`
       item with `account="slack.g0v"` represents a Slack channel
       whose name is defined in the item as `channel="..."`.)
3. Set up Zulip. You can follow [these
   instructions](https://github.com/42wim/matterbridge/wiki/Section-Zulip-%28basic%29).
   - Make note of the new bot's email and API key; you'll need those
     to set some Heroku environment variables later.
   - Don't forget to make sure that the streams you want to bridge (1)
     exist, and (2) are subscribed to by the new bot; otherwise,
     things will break. You can subscribe the bot using the stream's
     settings, under "Stream membership".
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
