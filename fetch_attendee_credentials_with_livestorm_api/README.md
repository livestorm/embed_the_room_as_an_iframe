## Fetch user credentials via tha API

This example shows how to interact from the embedding website with the Livestorm API in order to extract the attendee credentials provided that the session ID in Livestorm and the attendee's email is already known.

We assume that a user has been registered anonymously with the Livestom API,
and that the generated email address, of the form `1720628126+528d3ec6-b983-4353-bcbf-eddb1566d013@unknown.livestorm.co`
 has been stored somewhere. For details on how to register users with the API,
 see [here](https://github.com/livestorm/embed_the_room_as_an_iframe/blob/restrict-to-public-events/README.md#register-participants-as-anonymous-users-with-a-custom-email-address).

It is important to note that interacting directly with the Livestorm API from the HTML / JavaScript code like exposed in this example does not reflect what should be done in production, where a backend server would be more suited to send Livestorm API requests.

![image](https://github.com/livestorm/embed_the_room_as_an_iframe/assets/4329537/ec0ef666-c3df-4bff-804b-a79deddba75f)
