---
layout: post
title: 'Kodi: Send text to Kodi'
date: '2017-10-10 12:09:29'
---



```
curl -v -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"Input.SendText","params":{"text":"<text to send>","done":false},"id":1}' http://<host>:<port>/jsonrpc
```