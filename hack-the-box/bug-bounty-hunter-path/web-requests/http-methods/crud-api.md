# CRUD API

We saw examples of a `City Search` web application that uses PHP parameters to search for a city name in the previous sections. This section will look at how such a web application may utilize APIs to perform the same thing, and we will directly interact with the API endpoint.

***

## APIs

There are several types of APIs. Many APIs are used to interact with a database, such that we would be able to specify the requested table and the requested row within our API query, and then use an HTTP method to perform the operation needed. For example, for the `api.php` endpoint in our example, if we wanted to update the `city` table in the database, and the row we will be updating has a city name of `london`, then the URL would look something like this:

```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london ...SNIP...
```
