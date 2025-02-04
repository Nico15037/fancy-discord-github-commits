this is tutorial on how to make your commit messages all fancy in discord when using webhooks (someday I will upload images)

if you need help or have improvements, hmu at `isosaltset` on discord


4 steps - discord, github.com, making the embed, and the workflow file

------------------------------
1. DISCORD:

  go to server settings -> integrations -> webhooks
  click new webhook
  edit the picture, name, and channel (where it'll actually send the embed)

  //finished

  these all DO matter and are basically the "profile" of the person posting
  and unfortunately I can't find a way to just do it in the workflow file
  (like how the LAME default github webhook forcefully does it)
  hmu if you do find a way tho



------------------------------
2. GITHUB.COM:

  https://github.com

  go to your repo -> settings -> secrets and variables -> actions
  click "new repository secret"
  for the name "DISCORD_WEBHOOK"
  for the secret, paste in your webhook

  you can ALSO make the webhook color and image link into a secret

  //yeah that's it



------------------------------
3. MAKING THE EMBED:

  -----
  the discord part:
    https://discohook.org (has a live preview)
    when you've made it, go to JSON data editor (at the bottom) and copy the "embeds" section
    and make sure to remove the "[]" it's enclosed in

    here's an example of what you should have:
      {
        "title": "oh shit",
        "description": "fuck",
        "color": 15105570
      }

    "wtf is that color code format" discord uses ANSI color codes because they hate us

    anyways you can run it through https://jsonlint.com if you want to prettify it

  -----
  customize the embed based on the commit (the cool part):

    LIST OF VARIABLES (well the ones I know at least)
      commit.title
      commit.description
      commit.url
      commit.author.username
      commit.timestamp
    
  -----
  example of how to use them
    {
      "title": "{{ commit.title }}",
      "description": "{{ commit.author.username }} pushed this at {{ commit.timestamp }}. check out how he {{ commit.description }}",
      "color": 15105570
    }

  //the (painful) end

  -----
  CAN SKIP - side notes (aka reasons why I cry at night)

    • gamers I gotta be real I don't know where to find an actual list of these
    I literally brute forced it and made the embed print the entire payload with all variables so I could find what I want
    and that was I think years ago by now so I can't find where I dumped it or even remember how to do it
    I think you just make the desc {{ commit }} {{ github.context.payload }}? but .slice it or something bc it's fucking MASSIVE
    future nico: OK I JUST TRIED AND IDK HOW I DID IT. GOOD LUCK GUYS.

    • unfortunately idk how to change the webhook's icon (what looks like the message "sender") to something custom
    I think you'd have to mess with the actual github action's code (https://github.com/Sniddl/discord-commits)

    • I tried to see if there was a way to use a file in the repo for the embed thumbnail
    couldn't figure it out, so personally I just use https://cloudinary.com as the image host for the thumbnail
    if anyone DOES figure it out hmu PLEASE



------------------------------
THE WORKFLOW FILE (in the repo):

    -----
    in your repo's root folder:
        make a ".github" folder in your repo's root
        make a "workflows" folder
        now in ".github/workflows", make "discord-webhook.yml"

    -----
    editing "discord-webhook.yml":

        this uses https://github.com/Sniddl/discord-commits
        v3 because any past that make it say "Successful Commit to" BRO SHUT UP GET THE FUCK OUT OF HERE

        make sure the on: push: is set to the right branch (master/main/whatever it is)
        it'll be at the top of the file looking something like this:
        //file begin

        name: Discord Webhook Workflow
        on:
          push:
            branches: [ main ]

        jobs:
          postcommitmessage:
            runs-on: ubuntu-latest
            steps:
              - name: Discord Commits
                uses: Sniddl/discord-commits@v1.3
                with:
                  webhook: ${{ secrets.DISCORD_WEBHOOK }}
                  embed: '{}'

        //file end

        "webhook:" uses the github repo's secret that has your discord webhook in it
        "embed:" is the embed. it'll go inside the '{}', and make sure you have the '' around the {} or it won't work

  //mission complete

tl;dr just scroll to the bottom, copy paste that and throw in your embed



------------------------------
DUDE THIS SHIT ISN'T WORKING:

  • make sure there are no tabs, only spaces (.yaml files are whitespace-sensitive and don't allow tabs)
  • check your embed in jsonlint.com
  • make sure you made the webhook in your discord server
  • copy paste my example and just edit that it'll be easier I swear

  or just suffer and restart this tutorial from the top



------------------------------
CREDITS:

  https://github.com/WolfNT90
  he showed me this (thank you x1)
  made a little tutorial (thank you x2)
  deleted it the server it was in (unfortunate)
  refused to resend it if he still had it cause he's a TWAT (perish)
  but yk I still re-figured it out and made this tutorial (so legally thank you x3)

  https://github.com/Sniddlr
  they made the workflow action and are gods

  https://github.com/Nico15037
  me bitch

  shoutouts to the songs I listened to during this
  (they are why the commits are. interesting.)



------------------------------
WHAT I USE (NOT SPONSORED)
(BUT DEFINITELY NICO-APPROVED)

JUST COPY PASTE THIS INTO YOUR REPO'S discord-webhook.yml FILE

name: Discord Webhook Workflow
on:
  push:
    branches: [ main ]

jobs:
  postcommitmessage:
    runs-on: ubuntu-latest
    steps:
      - name: Discord Commits
        uses: Sniddl/discord-commits@v1.3
        with:
          webhook: ${{ secrets.DISCORD_WEBHOOK }}
          embed: '{
            "title": "{{ commit.title }}",
            "description": "{{ commit.description }}",
            "url": "{{ commit.url }}",
            "color": 12229743,
            "author": {
              "name": "{{ commit.author.username }}",
              "url": "https://github.com/{{ commit.author.username }}",
              "icon_url": "https://github.com/{{ commit.author.username }}.png"
            },
            "timestamp": "{{ commit.timestamp }}",
            "thumbnail": {
            "url": "https://LINK TO YOUR IMAGE HERE.COM/WHATEVER.png"
            }}'