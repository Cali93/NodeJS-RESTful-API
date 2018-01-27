# NodeJS-RESTful-API

## Dans ce projet, nous allons créer notre propre API qui permettra au coté client de faire les requêtes ci-dessous sur différentes routes.
```GET``` 
```POST```
```PUT```
```PATCH```
```DELETE```

## 1. Créez le projet :
```
mkdir NodeProjects
cd NodeProjects

git clone https://github.com/username/NodeJS-RESTful-API
cd NodeJS-RESTful-API

npm init
```

## 2. Installez les packages suivants (il est important de se documenter sur les différentes dépendences ci-dessous) :

```
npm install express
"i = raccourci pour install"
npm i body-parser
npm i bluebird
npm i mongoose

"il est aussi possible de les installer à la chaine :"
npm i jsonwebtoken passport-jwt passport bcryptjs cors
```
Dans le fichier package.json rajoutez la commande npm start :
```
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "nodemon server.js"
  }
 ```

## 3. Créez la structure de dossier :
```
├── api
│   ├── config
│   │   ├── database.js
│   │   ├── passport.js
│   ├── controllers
│   │   ├── user.js
│   ├── models
│   │   ├── user.js
│   ├── routes
│       ├── users.js
│
├── node_modules (dossier présent dès l'installation des packages)
│
├── app.js
├── server.js
├── package.json
├── package-lock.json
├── README.md
└── .gitignore
```

## 4. Dans api/config/databas.js, déterminez le lien de la DB.
```
module.exports = {
  database: "mongodb://localhost:27017/restapi",
  secret: "whatisthat"
}
```
## 5. Dans votre fichier app.js

### Commencez par importer les différents modules installés précédemment :

```
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');
const cors = require('cors');
const passport = require('passport');
const mongoose = require('mongoose');
const config = require('./api/config/database');
```

### Connectez-vous à la base de donnée :

```
mongoose.Promise = require('bluebird');
mongoose.connect(config.database, {
  promiseLibrary: require('bluebird')
  })
  .then(() => console.log(`Connected to database ${config.database}`))
  .catch((err) => console.log(`Database error: ${err}`));
```

### Utilisation d'Express en tant que framework
```
const app = express();
```
### Import de la route /users et définition du port
```
const users = require('./api/routes/users');
const port = 3000;
```

### Utilisation des différents middlewares
```
// CORS Middleware
app.use(cors());

// Paramétrage d'un dossier static pour les vues
app.use(express.static(path.join(__dirname, 'public')));

// Body Parser Middleware
app.use(bodyParser.json());

// Passport Middleware
app.use(passport.initialize());
app.use(passport.session());

require('./api/config/passport')(passport);
```

### Définition de la route qui s'occupera des requetes
```
app.use('/users', users);
```
### Indexation des routes
```
app.get('/', (req, res) => {
  res.send('Invalid Endpoint');
});

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'public/html'));
});
```

### Gestion d'erreurs
```
app.use((req, res, next) => {
  const error = new Error("Not found");
  error.status = 404;
  next(error);
});

app.use((error, req, res, next) => {
  res.status(error.status || 500);
  res.json({
    error: {
      message: error.message
    }
  });
});

```

### Exportation de l'objet app
```
module.exports = app;
```

## 6. Import de l'objet app dans le fichier server.js
```
const http = require('http');
const app = require('./app');

const port = process.env.PORT || 3000;

const server = http.createServer(app);

app.listen(port, () => {
  console.log('Server started on port ' + port);
});
```