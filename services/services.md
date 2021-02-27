# Services

Para manejo de los API calls se tiene el directiorio **services** en src/services/

En este directorio se manejan los servicios por API:

**src/services/habi-brokers-api.js**

Si el api tiene muchos endpoints, se pueden separar en directorios adicionales por categoria:

**src/services/habi-brokers-api/broker.js**

## Archivo de Services

Utilizamos **Axios** para nuestras llamadas de API. Este archivo debe incluir el **apiKey**, y el **rootUrl** que provienen de nuestro archivo de entorno **env-cmdrc**.

```javascript
import axios from "axios";

const apiKey = process.env.REACT_APP_HABI_API_BROKERS_KEY;
const rootUrl =
  process.env.REACT_APP_HABI_API_URL_V2 +
  process.env.REACT_APP_ENDPOINT_HABI_BROKERS_API_BASE_PATH;
```

## Servicios

Los servicios son funciones que hacen nuestro API Call. Estas funciones son un wrapper que construyen nuestro request donde compartimos rootUrl y apiKey. Como van a ser manejadas dentro de un **try/catch** block o un **then/catch**, solo tenemos que retornar el axios:

```javascript
export const getInitialBroker = async (email) => {
  const endpointUrl = process.env.REACT_APP_HABI_API_GET_INITIAL;
  const url = rootUrl + endpointUrl;
  const data = {
    mail: email,
    limit: 10,
    offset: 0,
  };
  return await axios.post(url, JSON.stringify(data), {
    headers: {
      "x-api-key": apiKey,
      "Content-Type": "application/json",
    },
  });
};
```

Dentro de nuestro thunk o useEffect vamos a manejar la respuesta o error, despachando acciones en Thunk, o manejo con estado del componente.
