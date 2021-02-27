# Reducers

**Location**: src/redux/stateName/

## Reducer file name

stateName.reducer.js

Example: user.reducer.js

##

El reducer intercepta las acciones y maneja el cambio del estado en un **case**. Tienen el valor del estado inicial para esa porcion del estado en la constante **INITIAL_STATE** e importan las acciones de los archivos de accion.

### Initial State

Para facilitar la modificacion del estado y contextualizarnos de como manejariamos los casos dentro del reducer.

```javascript
const INITIAL_STATE = {
  userData: {
    name: '',
    id: '',
  },
};
```

### Reducer

El reducer intercepta la accion y ejecuta la logica. **Siempre** debe llevar un caso default que retorne el estado.

```javascript
const userReducer = (state = INITIAL_STATE, { type, payload }) => {
  switch (type) {
    case UserActionTypes.SET_USER_DATA:
      return payload;
    // Otros casos
    default:
      return state;
  }
};

export default userReducer;
```

### Reducers con Thunk

Cuando hay estado que se coloca despues de la llamada de un API, tenemos que manejar los diferentes estados de un **Promise**.

```javascript
const INITIAL_STATE = {
  isFetching: false,
  userData: {
    name: '',
    id: '',
  },
  error: undefined,
};
```

#### Estados de promesa

1. Pending

Colocamos **isFetching** true.

2. Rejected

Colocamos **isFetching** false y enviamos el mensaje de error como payload para la propiedad **error** para manejar el caso de error.

3. Fullfiled/Resolved

Colocamos **isFetching** false y enviamos los datos de la respuesta en el payload para el estado.

#### Paginacion/Infinite scroll

Para poder acceder a los datos de paginacion para manejar peticiones, se crea un estado para guardar estos datos en INITIAL_STATE.

```javascript
const INITIAL_STATE = {
  isFetching: false,
  userData: {
    name: '',
    id: '',
  },
  userPagination: {
    page: 1,
    items: 10,
  },
  error: undefined,
};
```

## Root Reducer

El root-reducer es donde combinamos todas las porciones de estado de nuestros reducers y determinan la estructura del store. Mantenemos este archivo aparte del store en caso de que querramos persistir algun reducer o incorporar un **enhancer** en algun reducer.

```javascript
import { combineReducers } from 'redux';

import userReducer from './reducers/user.reducer';

const rootReducer = combineReducers({
  user: userReducer,
});

export default rootReducer;
```
