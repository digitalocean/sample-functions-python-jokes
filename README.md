## Using Python to implement a slash command

The most convenient way to create a slash command to use with [Nimbella Commander](https://nimbella.com/integrations/commander) is to run `/nc command_create` and implementing the command in the provided editor. However this is not convenient for commands that require third party libraries, or when you want to use source control to manage your code.

An alternate approach is to use the [Nimbella CLI called `nim`](https://docs.nimbella.com/install). With `nim` you can develop and test your commands locally, commit the code to GitHub (or your favorite source control), and then deploy the project either by running `nim project deploy` yourself, or using a CI/CD process (e.g., GitHub actions).

This `jokes` project is an example to illustrate how to create a slash command using Python as the programming language. The command generates jokes using the [`pyjokes`](https://pypi.org/project/pyjokes/) library.

## Project organization

There is only one command in this project. It is implemented by the `joke` API in [./packages/default/joke/__main__.py](./packages/default/joke/__main__.py). The code imports `pyjokes` (installed via [`requirements.txt`](./packages/default/joke/requirements.txt)), generates a new joke and formats the response for Commander.

## Installing the project with `nim`

Before creating the slash command, we need to deploy the project. This is usually done with `/nc csm_install` from your messaging environment (e.g., Slack or Microsoft Teams). However `csm_install` currently does not support Python implementations of commands. So to accomplish the same goals, we will create the API ourselves:

```bash
nim project deploy /path/to/demo-projects/jokes
```

The output from `nim project deploy` will resemble the following:
```
> nim project deploy jokes
Warning: found commands.yaml but no project.yml
Deploying project '/path/to/projects/my-command-sets/jokes'
  to namespace 'your-namespace'
  on host 'https://your-host.nimbella.io'
Started running ./build.sh in /path/to/demo-projects/jokes/default/joke
Finished running ./build.sh in /path/to/demo-projects/jokes/default/joke
Deployment status recorded in 'jokes/.nimbella'

Deployed actions ('nim action get <actionName> --url' for URL):
  - joke
```

The API is now live. We will need the URL for the API to use with [Commander as a webhook](https://nimbella.com/docs/commander/slack/reference#command_webhook). Running the command shown at the end of the project deploy command will produce the API endpoint you need.

```
nim action get joke --url
```

**Pro tip:** You can run `nim project deploy github:nimbella/demo-projects/jokes` to install the project directly from GitHub without cloning this project. The CLI may prompt you to login with GitHub to work around GitHub API rate limits. 

## Creating the slash command

Now you can create the command in your messaging environment. In Slack or Microsoft teams you will issue the following commands:
```
/nc command_create joke
/nc command_webhook joke <joke-url-from-previous-step>
```

Note the second command which binds the slash command `joke` to the URL of your API. This invokes your API every time your command runs. 

## Run your new command

You are ready to use the slash command now. You can run it, and administer it like any other slash command.
```
/nc joke
ASCII stupid question, get a stupid ANSI.
```
