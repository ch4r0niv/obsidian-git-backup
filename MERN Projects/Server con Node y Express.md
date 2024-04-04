## Crear archivo package.json

```
npm init -y
```
### Modificar package.json

```
"scripts": {
	"start": "nodemon index.js"
}
```

## Instalar Express

```
npm install express cors nodemon mongodb dotenv
```

## Crear archivo index.js

```
const express = require('express')
const app = express() 
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```

