arranco desde 0, para eso uso yeoman para que me cree la estructura del proyecto
encuentro este: https://github.com/stylesuxx/generator-react-webpack-redux

si no tengo yeoman
```
$ npm install -g yo
```
```
npm install -g generator-react-webpack-redux
```
```
$ mkdir tutorialRedux && cd tutorialRedux
$ yo react-webpack-redux
```
corro los test para ver que esté todo ok
```
$ npm test
```
Le voy a agregar mi primer Action

Lo que quiero hacer es crear una Agenda...para ello tengo una lista de charlas, cada charla tiene un id, ragoHorario, un título y un orador
```
$ yo react-webpack-redux:action AddCharla
```
Lo toqueteo un poco, le agrego un id, me queda así
Ahora hago un test para probarlo
no me anda el import del * 
```
import * as actions from '../../src/actions'
```
lo hago de la forma
```javascript
var actions = require('../../src/actions/charlas');
```

