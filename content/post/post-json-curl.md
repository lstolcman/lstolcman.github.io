---
title: "POSTing multiline JSONs by curl"
date: 2022-03-03T19:11:00+02:00
draft: false
categories: [tech]
tags: [http,curl,post]
---

Sending multiline POST requests by curl (in console):

```sh
curl -X POST 127.0.0.1:3000/api/v1/something/ -H "Content-Type: application/json; charset=utf-8" --data-binary @- << BODY
{
    "somefield1": "data1",
    "somefield2": "data2"
}
BODY
```

PS There was a nice GUI tool called Postman, unfortunately it became slow and bloated. Alternative: [Insomnia](https://github.com/Kong/insomnia). Version 2.5.0 is fine.
