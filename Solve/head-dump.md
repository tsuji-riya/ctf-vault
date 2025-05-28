#Easy #Web_Exploitation #picoCTF_2025 #browser_webshell_solvable 
# Overview
どうやらブログサイトとapiドキュメントがあるみたいです。
![[Pasted image 20250528151432.png]]

# Server Detail
サーバーサイドはnodejs、API DocumentはSwaggerで構成されている。

# How to Capture the flag
#easy 問題なのでかなりeasy

APIドキュメントを見ると、なんとメモリをダンプしてくれるみたい!
なんて脆弱な 🙈
![[Pasted image 20250528151631.png]]

dumpしたものをダウンロードして中身を調べてみると...
```sh
riya@riya-macair Downloads % cat heapdump-1748412104797.heapsnapshot | grep "CTF"

picoCTF{Pat!3nt_15_Th3_K3y_63fa652c}

"const express = require('express');\nconst heapdump = require('heapdump');\nconst fs = require('fs');\nconst path = require('path');\nconst { swaggerUi, specs } = require('./swagger');\n\nconst app = express();\napp.use(express.static('static'));\nconst port = process.env.NEWS_PORT || 3000;\n\napp.use(express.json()); \napp.set('view engine', 'ejs');\napp.use(express.static('public'));\n\n// Serve Swagger UI\napp.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));\n\n/**\n * @swagger\n * tags:\n *   - name: Free\n *     description: API endpoints for navigating the website.\n */\n\n\n/**\n * @swagger\n * /:\n *   get:\n *     tags:\n *       - Free\n *     summary: Welcome page\n *     responses:\n *       200:\n *         description: Returns a welcome message.\n */\napp.get('/', (req, res) => {\n  res.render('index', { title: 'Heapdump CTF Challenge API' });\n});\n\n/**\n * @swagger\n * /about:\n *   get:\n *     tags:\n *       - Free\n *     summary: About Us\n *     responses:\n *       200:\n *         description: Returns information about us.",

"Heapdump CTF Challenge API",

"\nwindow.onload = function() {\n  // Build a system\n  var url = window.location.search.match(/url=([^&]+)/);\n  if (url && url.length > 1) {\n    url = decodeURIComponent(url[1]);\n  } else {\n    url = window.location.origin;\n  }\n  var options = {\n  \"swaggerDoc\": {\n    \"openapi\": \"3.0.0\",\n    \"info\": {\n      \"title\": \"picoCTF News API\",\n      \"version\": \"1.0.0\",\n      \"description\": \"Welcome to the picoCTF News API documentation! This documentation provides a detailed overview of the available API endpoints for managing and retrieving news posts.\"\n    },\n    \"paths\": {\n      \"/\": {\n        \"get\": {\n          \"tags\": [\n            \"Free\"\n          ],\n          \"summary\": \"Welcome page\",\n          \"responses\": {\n            \"200\": {\n              \"description\": \"Returns a welcome message.\"\n            }\n          }\n        }\n      },\n      \"/about\": {\n        \"get\": {\n          \"tags\": [\n            \"Free\"\n          ],\n          \"summary\": \"About Us\",\n          \"responses\": {\n            \"200\": {\n              \"desc",

"picoCTF News API",

"Welcome to the picoCTF News API documentation! This documentation provides a detailed overview of the available API endpoints for managing and retrieving news posts.",
```
Gotcha!
フラグを掴むことができました🚩