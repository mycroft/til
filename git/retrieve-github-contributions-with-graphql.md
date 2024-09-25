# Retrieve Github contributions with GraphQL

The Github API provides an interface to retrieve a lot of informations about your account and especially daily contributions. This requires a simple github token (with `user:read` permissions)


## Retrieve with a simple shell/curl query

```sh
$ curl -H "Authorization: bearer <your token>" -X POST -d '{"query": "query { user(login: \"mycroft\") { contributionsCollection { contributionCalendar { totalContributions } } } }"}' https://api.github.com/graphql
{"data":{"user":{"contributionsCollection":{"contributionCalendar":{"totalContributions":1088}}}}}
```


## Complete Python code

```py
import requests
from datetime import datetime, timedelta
from collections import defaultdict

def get_contributions(username, start_date, end_date, access_token):
    query = """
    query ($username: String!, $from: DateTime!, $to: DateTime!) {
      user(login: $username) {
        contributionsCollection(from: $from, to: $to) {
          contributionCalendar {
            weeks {
              contributionDays {
                date
                contributionCount
              }
            }
          }
        }
      }
    }
    """

    variables = {
        "username": username,
        "from": start_date.isoformat(),
        "to": end_date.isoformat()
    }

    headers = {
        "Authorization": f"Bearer {access_token}",
        "Content-Type": "application/json",
    }

    response = requests.post('https://api.github.com/graphql', 
                             json={'query': query, 'variables': variables}, 
                             headers=headers)

    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"Query failed with status code: {response.status_code}. {response.text}")

username = 'mycroft'
access_token = '<your token>'
end_date = datetime.now()
start_date = end_date - timedelta(days=365)

try:
    data = get_contributions(username, start_date, end_date, access_token)
    monthly_contributions = group_contributions_by_month(data)
    
    for month, count in monthly_contributions.items():
        print(f"{month}: {count} contrib.")
except Exception as e:
    print(f"An error occurred: {e}")
```

This will output a simple dictionnary to parse to retrieve daily/monthly/yearly contributions.
