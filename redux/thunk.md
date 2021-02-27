# Redux Thunk

**Ubicacion**: src/redux/actions/

Thunk es un middleware para manejar acciones asincronicas relacionadas de estado que se usa en Redux. Generalmente se utiliza para operaciones **get**. Son **action creators** que despachan multiples acciones dependiendo del estado y respuesta de un **Promise** en un API request. La convencion es usar acciones con **START, SUCCESS, FAILURE**.

Thunk nos permite manejar como se renderiza un componente mientras se esta despachando una accion de API con el estado que agregamos **isFetching** para colocar un loader o deshabilitar un boton o un useEffect.

Los thunks se manejan en el archivo de **actions**.

## Acciones que usa thunk

Como estas acciones no se utilizan en los componentes la el nombre de la accion no necesita llevar el final **Action** porque no va a haber colision por nombre.

## Thunk sin parametros

```javascript
export const getUserAction = () => async (dispatch, getState) => {
  // getState() retorna los datos de nuestro store
  const userPagination = getState().user.userPagination;
  // dipatch dispara las acciones para cada estado de la promesa
  dispatch(userFetchStart());
  try {
    const res = await getUser();
    dispatch(userFetchSuccess(res.data));
  } catch (err) {
    dispatch(userFetchFailure(err.message));
  }
};
```

## Thunk con parametros

Un thunk puede recibir parametros que se le pasan a nuestro servicio del api. Estos parametros se le pasan en el componente como se haria en una funcion normal. De esta forma podemos manejar paginacion o parametros en nuestros requests si se estan manejando de manera local en el coponente.

```javascript
const getUserByIdAction = (userId) => async (dispatch) => {
  dispatch(userByIdFetchStartAction());
  try {
    const res = await getUserById(userId);
    dispatch(userByIdFetchSuccess(res.data));
  } catch (err) {
    dispatch(userByIdFetchFailure(err.message));
  }
};
```

## Thunk con paginacion en Redux

Si estamos guardando la paginacion en Redux, podemos usar getState, si se maneja localmente se utiliza un thunk con parametros.

```javascript
export const getUserAction = () => async (dispatch, getState) => {
  // getState() retorna los datos de nuestro store
  const userPagination = getState().user.userPagination;
  // dipatch dispara las acciones para cada estado de la promesa
  dispatch(usersFetchStart());
  try {
    const res = await getUser(userPagination);
    dispatch(usersFetchSuccess(res.data));
  } catch (err) {
    dispatch(usersFetchFailure(err.message));
  }
};
```

## Usar un Thunk

Para usar un thunk se importa la accion y se conecta por **mapDispatchToProps** como una accion normal.

```javascript
useEffect(() => {
  getUser();
}, [getUser]);
```

Esta accion despachara todos las acciones y podemos manejar los diferentes casos desde el store de Redux.

## Debounce con Thunk

Aveces tenemos requests al API que se disparan cuando se hace un cambio en un input. Para evitar una llamada con cada tecla o cambio del input podemos implementar un **debounce**. Un debounce espera una cantidad de tiempo **en ms** desde que dejo de cambiar el input para ejecutar el API request.

```javascript
import makeDebounce from "redux-debounce-thunk";

const getUserByIdThunk = (userId) => async (dispatch) => {
  dispatch(userByIdFetchStartAction());
  try {
    const res = await getUserById(userId);
    dispatch(userByIdFetchSuccess(res.data));
  } catch (err) {
    dispatch(userByIdFetchFailure(err.message));
  }
};

export const fetchUserByIdAction = makeDebounce(getUserByIdThunk, 1000);
```

Esta accion solo ejecutara el API call cuando el input lleva quieto 1 segundo. Esto es util para filtros que se disparan con un input que genera muchos cambios como un **range** input que puede generar decenas de cambios en menos de un segundo que activan el useEffect que ejecuta la llamada del API.
