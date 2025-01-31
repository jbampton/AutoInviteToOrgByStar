# Auto Invite To Orginization By Star

A GitHub action script to automatically invite everyone to your organization who star your repository. 

## What is this?

### You can STAR this repository, After that, you will invite to a surprise and fantastic organization.

## Deploy

#### Create `MY_GITHUB_KEY` variable at Secrets

Open https://github.com/settings/tokens and create one.

#### Create `COMMUNITY_TEAM_ID` variable at Secrets

Do not have team id at your org?

Running:

```
curl -H "Authorization: token *****" https://api.github.com/orgs/YOUR_ORG_NAME/teams
```
p.s: Put your personal token at the `****`, and replace your org name at the `YOUR_ORG_NAME`.


#### Create `.github/workflows/invite-by-star.yml` file:

```yaml
on:
  watch:
    types: [started]
name: Invite a new user
jobs:
    build:
      runs-on: ubuntu-latest

      steps:
        - name: checkout repo content
          uses: actions/checkout@v2
        - name: setup python
          uses: actions/setup-python@v2
          with:
            python-version: 3.8
        - name: Install dependencies
          run: |
            python -m pip install --upgrade pip
            pip install requests
            if [ -f ./.github/workflows/requirements.txt ]; then pip install -r requirements.txt; fi
        - name: execute py script
          run: |
            python ./.github/workflows/run.py
          env:
            MY_GITHUB_KEY: ${{ secrets.MY_GITHUB_KEY }}
            COMMUNITY_TEAM_ID: ${{ secrets.COMMUNITY_TEAM_ID }}
```

### Create `.github/workflows/run.py` file

```
# Max Base
# 2021-06-19
# https://github.com/BaseMax/AutoInviteToOrgByStar

import os
import sys
import json
import requests

print("Hello, World")

if os.getenv('CI'):
    print('Looks like GitHub!')
else:
    print('Maybe running locally?')

print("Environ:")
print(os.environ)
print("Prefix:")
print(sys.prefix)

MY_GITHUB_KEY = os.environ['MY_GITHUB_KEY']
COMMUNITY_TEAM_ID = os.environ['COMMUNITY_TEAM_ID']

file = open(os.environ['GITHUB_EVENT_PATH'])
data = json.load(file)

print("Data:")
print(data)

USERNAME = data['sender']['login']

print('Send invite for the @'+USERNAME)

# TODO: check user already joined or no....
url = 'https://api.github.com/teams/'+COMMUNITY_TEAM_ID+'/memberships/' + USERNAME
payload=''
headers = {
    'Accept': 'application/vnd.github.v3+json',
    'Authorization': 'token '+MY_GITHUB_KEY
}
response = requests.request("PUT", url, headers=headers, data=payload)
print(response.text)
```

© Copyright Max Base, 2021
