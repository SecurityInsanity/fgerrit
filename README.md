fgerrit
=======

Why ? Because fark the gerrit web ui thats why. :goberserk:

## Wtf is this ?

A simple git extension that makes interacting with gerrit less painful by
wrapping the gerrit ssh api. Use it to:

-1 :sparkles:ALL:sparkles: the reviews!

...or you know, list review's, approve them, whatever floats your boat.

## Configuration
fgerrit will try to pull gerrit information automagically via your git config. To do so it checks to see if you have a gerrit remote branch by running a `git config --get remote.gerrit.url`. If you don't have gerrit a remote you can pass in the options at the cli. Or better yet, set the env variable:

`export GERRIT_URL=ssh://username@gerrit.host.com:29418/yourproject`

If you're like me you probably use a gerrit alias in your SSH Config so you can do: `git clone gerrit:repo` this breaks the
default finding of a repo. As such you can work around it with a neat bash function in your bashrc:

```bash
# Utilize fgerrit
fgerrit() {
  local project_url="$(git config --local --get remote.origin.url)"
  if [[ "$project_url" =~ ^gerrit:* ]]; then
    local project_name="$(echo "$project_url" | cut -d : -f 2)"
    git fgerrit --host "<host>" --port <port> --user "<user>" --project "$project_name" "$@"
  else
    git fgerrit --host "<host>" --port <port> --user "<user>" "$@"
  fi
}
```

Then you can run commands like: `fgerrit`, `fgerrit <id>`, and `fgerrit <id> show`.

## Usage
It's just a git extension, just give it a try in a repo that uses gerrit. Heres a few quick examples to get you started.

List all open reviews:

	fhines@ubuntu:~/omgmonkeys(master)$ git fgerrit -l`
	====================================================
	= Open reviews for unicorns/meatgrinder       	   =
	====================================================
	id    	(date ) [V|C|A] "commit subject" - submitter
	----------------------------------------------------
	9kl1V	(02:31) [1|-2|0] "Fix bug: #121 snakes on plane" - sammyj
	7b251	(08/21) [1|1|0] "Implements a basic unicorn grinder" - pandemicsyn

Wanna see info on the review with the shortid 7b251 from above?

	git fgerrit 7b251

Is id 7b251 an abomination, wanna -1 that crap?

	git fgerrit 7b251 -1

Need to drop some knowledge on a patch set?

	git fgerrit 7b251 post "switches love packets"

Here's all the options:

    Usage: git-fgerrit: [options] [<command> [<arguments>]]

    Gerrit information defaults to gerrit remote url in git config or the
    GERRIT_URL env. The options --host, --user, --port, --project will override
    these defaults.

    Examples:
        List all pending reviews:  git-fgerrit
               View review 7b251:  git-fgerrit 7b251
            To -1 patchset 7b251:  git-fgerrit 7b251 -1

    Avaliable Commands:
        <nothing>                lists pending reviews
        mark                     touches the file in .fgerrit-mark which is used by
                                 the above listing to only show newer changes
        <id>                     shows review information
        <id> show                like git-show but for the patch set <id>,
                                 note that this will change your FETCH_HEAD
        <id> checkout [patch#]   checks out the code for a review; the optional
                                 [patch#] lets you checkout a specific patchset
                                 note that this will change your FETCH_HEAD
        <id> diffsince [patch#]  shows a diff of what you have checked out locally
                                 vs. the code at <id>
                                 [patch#] lets you diff a specific patchset
                                 note that this will change your FETCH_HEAD
        <id> post     [message]  posts a message to a review
        <id> -2       [message]  indicates a strong "do not merge" opinion
        <id> -1       [message]  indicates a normal "do not merge" opinion
        <id>  0       [message]  indicates a neutral opinion
        <id> +1       [message]  indicates a non-core-reviewr positive opinion
        <id> +2       [message]  indicates a core-reviewer positive opinion
        <id> approve  [message]  requests the change be merged
        <id> recheck             asks for the change to be rechecked
        <id> reverify            asks for the change to be reverified
        <id> draft    [message]  indicates the change is a work-in-progress
        <id> abandon  [message]  requests the change be removed from review
        <id> delete   [message]  requests the change be removed from the system
        <id> restore             requests the change be restored for review
             submit              submits the current branch for review
             draft               submits the current branch as a work-in-progress

    If [message] is not specified, your $FGERRIT_EDITOR or $EDITOR will be loaded
    up for you create a message. If you save an empty message, the command will be
    aborted. You can use a single dash as the message if you truly want no message.

    Options:
      -h, --help           show this help message and exit
      --host=HOST          Gerrit hostname or ip
      --port=PORT          Gerrit port
      --user=USER          Gerrit user
      --project=PROJECT    Gerrit project
      --wip                Include works-in-progress in the listing of pending
                           reviews
      --branches=BRANCHES  The branch names to include in the listing of pending
                           reviews; defaults to just master.

## Installation

- `git clone http://github.com/pandemicsyn/fgerrit fgerrit`
- `cd fgerrit`

Now either install via setup.py or build a debian package with [stdeb](https://github.com/astraw/stdeb)

- via setup.py
	- `sudo python setup.py install`

This will drop the git-fgerrit command into /usr/local/bin so it'll get picked up by git.

