# Sachverhalt Specification

## Common Primitives

* `website`: String containing a URL to a website, without `http://` or `https://`

## Common Objects

### Person

* `name`: The person’s name
* `website` (optional)
* `email` (optional): The person’s email address

#### Example

```json
{
  "name": "shak-mar",
  "email": "shakmar@openmailbox.org",
  "website": "github.com/shak-mar"
}
```

### Plugin

* `name`: The plugin’s name
* `version`: The plugin’s version
* `compatible`: A Boolean.
* `authors` (may be empty): List of persons
* `website` (optional)
* `synopsis` (optional): A String containing a short description of the plugin

#### Example

```json
{
  "name": "comments",
  "version": "1.0.0",
  "website": "github.com/shak-mar/comments",
  "synopsis": "Adds comments to issues",
  "authors": [ {
    "name": "shak-mar",
    "email": "shakmar@openmailbox.org",
    "website": "github.com/shak-mar"
  } ]
}
```

### Issue

* `title`: The title of the issue
* `description` (may be empty): String containing the description, may contain newlines
* `tags` (may be empty): List of Strings
* `creation_time`: A point of UTC time in RFC3339 format, but without the `+0:00`
* `status`: String containing `open` or `closed`
* `id`: String containing a UUID
* `plugins` (may be empty): List of Plugins that store extra information in this issue
* `author`: A person

#### Example

```json
{
  "title": "Write Examples",
  "description": "Examples are nice.\nWe need them to understand",
  "tags": [ "documentation", "project:issues_spec" ],
  "creation_time": "2015-05-06 18:29:57",
  "status": "open",
  "id": "9b85a84f-9154-4547-a137-c68ec6664e40",
  "plugins": [],
  "author": {
    "name": "shak-mar",
    "email": "shakmar@openmailbox.org",
    "website": "github.com/shak-mar"
  }
}
```

### FilterCriterion

* `type`: The criterion type.

#### Standard criterion types

##### `template`

* `template`: An Issue object with all fields optional.

Issues will match if all of the given fields in `template` are subsets.

##### `not`

* `criterion`: A FilterCriterion

Issues will match if they are not matched by `criterion`.

##### `and`

* `criteria`: A list of FilterCriterion objects

Issues will match if all of the `criteria` match.

##### `or`

* `criteria`: A list of FilterCriterion objects

Issues will match if at least one of the `criteria` matches.

#### Example

The following FilterCriterion will match Issues with the tag `question`, but not
from an author with the name `annoyingguy`:

```json
{
  "type": "and",
  "criteria": [ {
      "type": "template",
      "tags": [ "question" ]
    }, {
      "type": "not",
      "criterion": {
        "type": "template",
        "author": {
          "name": "annoyingguy"
        }
      }
    }
  ]
}
```

## Requests

### `query_plugins`

Is always successful. Adds the following fields to the response:

* `plugins`: List of activated plugins

### `open_issue`

Request:

* `issue`: The Issue object, without the `id` field.

Response:

* `id`: ID of the created Issue

### `edit_issue`

Request:

* `issue`: An Issue object with all fields but `id` optional. Existing fields will override those in the Issue corresponding to `id`.

### `filter_issues`

Request:

* `criterion`: A FilterCriterion

Response:

* `matching`: A list of Strings containing the `id` field of the matching Issues

### `fetch_issue`

Request:

* `id`: A String containing the `id` field of the requested Issue

Response:

* `issue`: The requested Issue
