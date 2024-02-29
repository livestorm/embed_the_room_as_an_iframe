# embed_the_room_as_an_iframe

This repository contains a set of sample HTML / JavaScript examples to embed
Livestorm sessions within an external webiste.

## Pre-requisites

In order to embed a Livestorm session within an external website, a team member
must explicitly configure the following:
- define the external URL allowed to embed the session
    - this is done on a per session basis using the Livestorm API
- configure his embedding web page to build the `<iframe />` DOM element that
  will hold the Livestorm session

### define the external URL to embed a Livestorm session

The URL to embed a Livestorm session can be set via a [the REST API](https://developers.livestorm.co/reference).

In the example below, we explicitly allow the website URL `https://my.external.domain.co/embedding_page.html`
to embed the Livestorm session identified with `657bf7da-3d26-4865-8284-35bd882539ff`.

The `JWT` variable must contain the Livestorm API token that allows to manage
the workspace. Example:
```
export JWT="eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJhcGkubGl2ZXN0b3JtLmNvIiwianRpIjoiNDc2ZDE0ZWEtZDVkMC00MjE2LThhODktYWM2YTUzMzQ1ZGUxIiwiaWF0IjoxNzA4MDcyODMxLCJvcmciOiI3ZGQ5ZWNlYi0wYzFjLTQzNzItODJmNS1kMmM3NmVjY2Y4ZTEifQ.DAfyU2srJ79zRD8_wdGqRwdkpIP_AVlZQA_g9a4jAbk"
```

Set the `embed_url` field for session `657bf7da-3d26-4865-8284-35bd882539ff`:
```
curl --request PATCH \
     --url https://api.livestorm.co/v1/sessions/657bf7da-3d26-4865-8284-35bd882539ff \
     --header "Authorization: ${JWT}" \
     --header 'accept: application/vnd.api+json' \
     --header 'content-type: application/vnd.api+json' \
     --data '
{
  "data": {
    "type": "sessions",
    "attributes": {
      "embed_url": "https://my.external.domain.co/embedding_page.html"
    }
  }
}
'
```

Set multiple session fields at once:
```
curl --request PUT \
     --url https://api.livestorm.co/v1/sessions/657bf7da-3d26-4865-8284-35bd882539ff \
     --header "Authorization: ${JWT}" \
     --header 'accept: application/vnd.api+json' \
     --header 'content-type: application/vnd.api+json' \
     --data '
{
  "data": {
    "type": "sessions",
    "attributes": {
      "timezone": "Europe/Paris",
      "estimated_started_at": "2025-01-12 00:00:00",
      "embed_url": "https://my.external.domain.co/embedding_page.html"
    }
  }
}
'
```

## Examples

### [fetch_attendee_credentials_with_livestorm_api](https://github.com/livestorm/embed_the_room_as_an_iframe/tree/main/fetch_attendee_credentials_with_livestorm_api)
