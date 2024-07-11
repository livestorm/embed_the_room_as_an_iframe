# embed_the_room_as_an_iframe

This repository contains a set of sample HTML / JavaScript examples to embed
Livestorm sessions within an external webiste.

## Pre-requisites

In order to embed a Livestorm session within an external website, a team member
must explicitly configure the following:
- make the event publicly accessible only
    - this can be done from the Livestorm dashboard and will affect all the
      sessions for the event. See [below](#make-the-event-publicly-accessible-only)
- define the external URL allowed to embed the session
    - this is done on a per session basis using the Livestorm API, as described [below](#define-the-external-url-to-embed-a-livestorm-session)
- register participants as anonymous users with a custom email address
    - this can be done with the Livestorm API, as described [below](#register-participants-as-anonymous-users-with-a-custom-email-address)
- configure the embedding web page to build the `<iframe />` DOM element that
  will hold the Livestorm session
    - see the [Examples](#examples] section below

### make the event publicly accessible only

In order for an event to be publicly accessible, go to the Registration settings
of the event and remove the `email` field from the list of required registration
fields.

Note that, in that case, you would still be able to register participants to the
event with a unique link, but the provided email address would be anonymised to
something like `1720541770+a3ca5465-bc23-4999-b28d-03a23eb46a94@unknown.livestorm.co`

![image](https://github.com/livestorm/embed_the_room_as_an_iframe/assets/4329537/0398deb0-1494-4373-8bff-0118a2d1a289)

### define the external URL to embed a Livestorm session

The URL to embed a Livestorm session can be set with [the REST API](https://developers.livestorm.co/reference).

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

### register participants as anonymous users with a custom email address

Users registering to a session of a public event will be anonymously registered,
meaning that they won't receive a unique connection link to join the Livestorm
session in their mailbox, just like registrants to regular events do. However,
using the Livestorm API, users registered anonymously will still receive a unique
connection link, that will contain an automatically generated email address, e.g.:

Connection link:
``` 
https://app.livestorm.co/p/fe9e2c48-a993-4d81-afbe-ed483f2d3576/live?email=1720628126%2B528d3ec6-b983-4353-bcbf-eddb1566d013%40unknown.livestorm.co&key=b794f5a32ad48829366806&s=0abbcb51-4a95-46d7-9879-fd141986a3a8
```
Email address:
```
1720628126+528d3ec6-b983-4353-bcbf-eddb1566d013@unknown.livestorm.co
```

Working with an anonymised unique connection link that does not contain the
actual user email address can be an issue to track down the attendance of the
Livestorm session for instance. Since the email address is often used as a key
to identify a user, we suggest to register users with a custom attribute that
will contain the actual user email address, therefore allowing you to tag the
user joining the session with his actual email address.

A custom email address can be used as a registration attribute in the
registration settings of the event. Here, the `Custom Email` attribute has been
added as a required field to register to the event.

Custom attribute creation:
![image](https://github.com/livestorm/embed_the_room_as_an_iframe/assets/4329537/de7f5f66-3df9-474e-a1dc-f1f403e597cf)

Set custom attribute as a registration field:
![image](https://github.com/livestorm/embed_the_room_as_an_iframe/assets/4329537/790e7857-56a8-4997-9dd3-622d4bcf60dc)

When done, using the Livestorm API, you'd be able to register the user with the
registration attributes, and get a response back from Livestorm with the unique
`connection_link` that matches with the participant.

Request:
```
curl --request POST \
     --url https://api.livestorm.co/v1/sessions/0abbcb51-4a95-46d7-9879-fd141986a3a8/people \
     --header "Authorization: ${JWT}" \
     --header 'accept: application/vnd.api+json' \
     --header 'content-type: application/vnd.api+json' \
     --data '
{
  "data": {
    "type": "people",
    "attributes": {
      "fields": [
        {
          "id": "first_name",
          "value": "Jane"
        },
        {
          "id": "last_name",
          "value": "Adams"
        },
        {
          "id": "custom_email",
          "value": "jane.adams@example.com"
        }
      ]
    }
  }
}
```

Response:
```
{
  "data": {
    "id": "fd0fc320-477f-4c56-9a16-28d6eecbbf96",
    "type": "people",
    "attributes": {
      "role": "participant",
      "created_at": 1720628127,
      "updated_at": 1720628127,
      "timezone": null,
      "first_name": "Jane",
      "last_name": "Adams",
      "email": "1720628126+528d3ec6-b983-4353-bcbf-eddb1566d013@unknown.livestorm.co",
      "avatar_link": null,
      "registrant_detail": {
        "event_id": "fe9e2c48-a993-4d81-afbe-ed483f2d3576",
        "session_id": "0abbcb51-4a95-46d7-9879-fd141986a3a8",
        "created_at": 1720628127,
        "updated_at": 1720628127,
        "fields": [
          {
            "id": "first_name",
            "type": "text",
            "value": "Jane",
            "required": true
          },
          {
            "id": "last_name",
            "type": "text",
            "value": "Adams",
            "required": true
          },
          {
            "id": "custom_email",
            "type": "text",
            "value": "jane.adams@example.com",
            "required": true,
            "custom": true
          }
        ],
        "referrer": null,
        "utm_source": null,
        "utm_medium": null,
        "utm_term": null,
        "utm_content": null,
        "utm_campaign": null,
        "browser_version": null,
        "browser_name": null,
        "os_name": null,
        "os_version": null,
        "screen_height": null,
        "screen_width": null,
        "ip_city": null,
        "ip_country_code": null,
        "ip_country_name": null,
        "password_key": "b794f5a32ad48829366806",
        "connection_link": "https://app.livestorm.co/p/fe9e2c48-a993-4d81-afbe-ed483f2d3576/live?email=1720628126%2B528d3ec6-b983-4353-bcbf-eddb1566d013%40unknown.livestorm.co&key=b794f5a32ad48829366806&s=0abbcb51-4a95-46d7-9879-fd141986a3a8",
        "attended": false,
        "attendance_rate": null,
        "attendance_duration": 0,
        "has_viewed_replay": false,
        "registration_type": "API",
        "is_highlighted": false,
        "is_guest_speaker": false
      },
      "messages_count": 0,
      "questions_count": 0,
      "votes_count": 0,
      "up_votes_count": 0,
      "replay_view_detail": null
    }
  }
}
````

## Examples

### [fetch_attendee_credentials_with_livestorm_api](https://github.com/livestorm/embed_the_room_as_an_iframe/tree/main/fetch_attendee_credentials_with_livestorm_api)
