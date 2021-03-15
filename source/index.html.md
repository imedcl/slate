---
title: Imed

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

search: true

code_clipboard: true
---

# CIE - API endpoints

## /diagnostics

This endpoint retrieves the diagnostic that have the code.

> Definition

```
GET http://localhost:3700/diagnostics?code=Z945&description=piel
```

> Example request

```shell
curl -X GET "http://localhost:3700/diagnostics?code=Z945&description=piel" -H  "accept: application/json"
```

```javascript
const show = async (req, res) => {
  const objects = Object.keys(req.query);
  switch (String(objects)) {
    case "code":
      // eslint-disable-next-line consistent-return
      Diagnostic.find({ code: req.query.code }, "-_id", (err, diagnostic) => {
        if (err) {
          return res
            .status(400)
            .json({ code: 400, error: { message: "#{err}" } });
        }
        if (diagnostic.length === 0) {
          return res
            .status(404)
            .json({ err: 404, error: { message: "Not found" } });
        }
        res.status(200).json({ code: 200, data: { diagnostic } });
      });
      break;
    case "description":
      // eslint-disable-next-line consistent-return
      Diagnostic.find(
        { description: { $regex: `.*${req.query.description}.*` } },
        "-_id",
        (err, description) => {
          if (err) {
            return res
              .status(400)
              .json({ code: 400, error: { message: "#{err}" } });
          }
          if (description.length === 0) {
            return res
              .status(404)
              .json({ err: 404, error: { message: "Not found" } });
          }
          res.status(200).json({ code: 200, data: { description } });
        }
      );
      break;
    default:
      res.status(400).json({ error: { mensaje: "Parametro no encontrado" } });
  }
};
```

> Example response

```json
{
  "code": "200",
  "data": [
    {
      "code": "Z945",
      "description": "Trasplante de piel"
    }
  ]
}
```

> Example error response

```json
{
  "code": 400,
  "error": {
    "mensaje": "Solo se puede enviar 1 parametro code o description"
  }
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "mensaje": "Debe enviar al menos 1 parametro code / description"
  }
}
```

### Query Parameters

| Parameter   | Description                                       | Data type |
| ----------- | ------------------------------------------------- | --------- |
| code        | Diagnostic code                                   | Data type |
| description | A word that the diagnostic description must have. | Data type |

## /diagnostics_search

This endpoint retrieves all diagnostics that content a word or code.

> Definition

```
GET http://localhost:3700/diagnostics_search?code=Z945&description=piel
```

> Example request

```shell
curl -X GET "http://localhost:3700/diagnostics_search?code=Z945&description=piel" -H  "accept: application/json"
```

```javascript
const search = async (req, res) => {
  const toSearch = {};
  if ("code" in req.query) toSearch.code = { $regex: `.*${req.query.code}.*` };
  if ("description" in req.query)
    toSearch.description = { $regex: `.*${req.query.description}.*` };
  // eslint-disable-next-line consistent-return
  Diagnostic.find(toSearch, "-_id", (err, description) => {
    if (err) {
      return res.status(400).json({ code: 400, error: { message: "#{err}" } });
    }
    if (description.length === 0) {
      return res
        .status(404)
        .json({ err: 404, error: { message: "Not found" } });
    }
    res.status(200).json({ code: 200, data: { description } });
  });
};
```

> Example response

```json
{
  "code": 200,
  "data": {
    "description": [
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      },
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      },
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      }
    ]
  }
}
```

> Example response when the two conditions fits

```json
{
  "code": "200",
  "data": [
    {
      "code": "Z945",
      "description": "Trasplante de piel"
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "mensaje": "code debe ser Alphanumerico"
  }
}
```

### Query Parameters

| Parameter   | Description                                       | Data type |
| ----------- | ------------------------------------------------- | --------- |
| code        | Diagnostic code                                   | String    |
| description | A word that the diagnostic description must have. | String    |

## /diagnostics_new_search

This endpoint retrieves all diagnostics that content a word or code.

> Definition

```
GET http://localhost:3700/diagnostics_new_search?code=Z945&description=piel
```

> Example request

```shell
curl -X GET "http://localhost:3700/diagnostics_new_search?code=Z945&description=piel" -H  "accept: application/json"
```

```javascript
const newSearch = async (req, res) => {
  let descUpper = "";
  let descLower = "";
  let descCapital = "";

  if ("code" in req.query) {
    req.query.code = req.query.code.trim();
    req.query.code = req.query.code.toUpperCase();
  }
  if ("description" in req.query) {
    req.query.description = req.query.description.trim();
    descUpper = req.query.description.toUpperCase();
    descLower = req.query.description.toLowerCase();
    descCapital =
      req.query.description.charAt(0).toUpperCase() +
      req.query.description.slice(1).toLowerCase();
  }

  const toSearch = {};
  if ("code" in req.query) toSearch.code = { $regex: `.*${req.query.code}.*` };
  if ("description" in req.query)
    toSearch.description = { $regex: `.*${descUpper}.*` };
  // eslint-disable-next-line consistent-return
  let description = [];
  Diagnostic.find(toSearch, "-_id", (err, description0) => {
    if (err) {
      return res.status(400).json({ code: 400, error: { message: "#{err}" } });
    }
    if (
      req.query.description === undefined &&
      req.query.description === null &&
      req.query.description.length === 0
    ) {
      return res
        .status(404)
        .json({ err: 404, error: { message: "Not found" } });
    }
    if (description.length === 0 && description0.length > 0) {
      description = description0;
    } else if (description.length > 0 && description0.length > 0) {
      description = description.concat(description0);
    }
    if ("description" in req.query) {
      toSearch.description = { $regex: `.*${descLower}.*` };
      Diagnostic.find(toSearch, "-_id", (err1, description1) => {
        if (err1) {
          return res
            .status(400)
            .json({ code: 400, error: { message: "#{err}" } });
        }
        if (description.length === 0 && description1.length > 0) {
          description = description1;
        } else if (description.length > 0 && description1.length > 0) {
          description = description.concat(description1);
        }
        if ("description" in req.query)
          toSearch.description = { $regex: `.*${descCapital}.*` };
        Diagnostic.find(toSearch, "-_id", (err2, description2) => {
          if (err2) {
            return res
              .status(400)
              .json({ code: 400, error: { message: "#{err}" } });
          }
          if (description.length === 0 && description2.length > 0) {
            description = description2;
          } else if (description.length > 0 && description2.length > 0) {
            description = description.concat(description2);
          }
          if (description.length === 0) {
            return res
              .status(404)
              .json({ err: 404, error: { message: "Not found" } });
          }
          return res.status(200).json({ code: 200, data: { description } });
        });
      });
    } else {
      return res.status(200).json({ code: 200, data: { description } });
    }
  });
};
```

> Example response

```json
{
  "code": 200,
  "data": {
    "description": [
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      },
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      },
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      }
    ]
  }
}
```

> Example response when the two conditions fits

```json
{
  "code": "200",
  "data": [
    {
      "code": "Z945",
      "description": "Trasplante de piel"
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "mensaje": "code debe ser Alphanumerico"
  }
}
```

### Query Parameters

| Parameter   | Description                                       | Data type |
| ----------- | ------------------------------------------------- | --------- |
| code        | Diagnostic code                                   | String    |
| description | A word that the diagnostic description must have. | String    |

## /readiness

This endpoint retrieves the readiness probe result.

> Definition

```
GET http://localhost:3700/readiness
```

> Example request

```shell
curl -X GET "http://localhost:3700/readiness" -H  "accept: application/json"
```

```javascript
async function readinessCallback(res) {
  if (!(await validations.readinessProbe())) {
    return res.status(500).send({ message: "readiness ERROR" });
  }
  return res.status(200).send({ message: "readiness OK" });
}
```

> Example response

```json
{
  "message": "readiness OK"
}
```

> Example error response

```json
{
  "message": "readiness ERROR"
}
```

## /liveness

This endpoint retrieves the liveness probe result.

> Definition

```
GET http://localhost:3700/liveness
```

> Example request

```shell
curl -X GET "http://localhost:3700/liveness" -H  "accept: application/json"
```

```javascript
async function livenessCallback(res) {
  if (!(await validations.livenessProbe())) {
    return res.status(500).send({ message: "liveness ERROR" });
  }
  return res.status(200).send({ message: "liveness" });
}
```

> Example response

```json
{
  "message": "liveness"
}
```

# Initial Settings - API endpoints

## /v1/configs/:country

This endpoint retrieves a JSON that contains data of the country.

> Definition

```
GET http://localhost:4000/v1/configs/EC
```

> Example request

```shell
curl --location --request GET 'http://localhost:4000/v1/configs/EC' \
--header 'accept: application/json'
```

```javascript
const show = async (req, res) => {
  try {
    const search = req.params.country.toUpperCase();
    let response = await redis.find(search);
    if (response !== null) {
      return res.status(200).json({
        code: "200",
        data: JSON.parse(response),
      });
    }
    return res.status(400).json({
      code: "400",
      error: {
        message: `Configuration does not exists for ${search}`,
      },
    });
  } catch (error) {
    return res.status(400).json({
      code: "400",
      error: {
        message: "An error has ocurred",
      },
    });
  }
};
```

> Example response

```json
{
  "code": "200",
  "data": {
    "country": "EC",
    "domainTable": [
      {
        "name": "sexo",
        "data": [
          {
            "value": "Masculino"
          },
          {
            "value": "Femenino"
          },
          {
            "value": "Otro"
          }
        ]
      },
      {
        "name": "estado civil",
        "data": [
          {
            "value": "soltero"
          },
          {
            "value": "casado"
          },
          {
            "value": "viudo"
          }
        ]
      }
    ],
    "settings": [
      {
        "name": "tipo_documento_personas",
        "data": [
          {
            "name": "CI",
            "value": "CI"
          }
        ]
      },
      {
        "name": "algoritmo_documento_personas",
        "data": [
          {
            "name": "mod10",
            "value": "mod10"
          }
        ]
      },
      {
        "name": "tipo_documento_instituciones",
        "data": [
          {
            "name": "RUC",
            "value": "RUC"
          }
        ]
      },
      {
        "name": "algoritmo_documento_instituciones",
        "data": [
          {
            "name": "mod10",
            "value": "mod10"
          }
        ]
      },
      {
        "name": "nombre_moneda",
        "data": [
          {
            "name": "DOLAR",
            "value": "USD"
          }
        ]
      },
      {
        "name": "signo_moneda",
        "data": [
          {
            "name": "$",
            "value": "$"
          }
        ]
      },
      {
        "name": "numero_decimales",
        "data": [
          {
            "name": "2",
            "value": "2"
          }
        ]
      },
      {
        "name": "divisionGeografica",
        "data": [
          {
            "name": "Nivel 1",
            "value": "provincias"
          },
          {
            "name": "Nivel 2",
            "value": "cantones"
          },
          {
            "name": "Nivel 3",
            "value": "parroquías"
          }
        ]
      }
    ]
  }
}
```

> Example error response

```json
{
  "code": "400",
  "error": {
    "message": "Configuration does not exists for EC"
  }
}
```

### Query Parameters

| Parameter | Description          | Data type |
| --------- | -------------------- | --------- |
| country   | Country abbreviation | String    |

## /readiness

This endpoint retrieves the readiness probe result.

> Definition

```
GET http://localhost:4000/readiness
```

> Example request

```shell
curl -X GET "http://localhost:4000/readiness" -H  "accept: application/json"
```

```javascript
async function readinessCallback(res) {
  if (!(await validations.readinessProbe())) {
    return res.status(500).send({ message: "readiness ERROR" });
  }
  return res.status(200).send({ message: "readiness OK" });
}
```

> Example response

```json
{
  "message": "readiness OK"
}
```

> Example error response

```json
{
  "message": "readiness ERROR"
}
```

## /liveness

This endpoint retrieves the liveness probe result.

> Definition

```
GET http://localhost:4000/liveness
```

> Example request

```shell
curl -X GET "http://localhost:4000/liveness" -H  "accept: application/json"
```

```javascript
async function livenessCallback(res) {
  if (!(await validations.livenessProbe())) {
    return res.status(500).send({ message: "liveness ERROR" });
  }
  return res.status(200).send({ message: "liveness" });
}
```

> Example response

```json
{
  "message": "liveness"
}
```

# Regional Division Ecuador - API endpoints

## /v1/countries/

This endpoint retrieves all the countries in database.

> Definition

```
GET http://localhost:3900/v1/countries/
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/'
```

```javascript
const showCountries = (req, res) => {
  Countries.getAll()
    .then((data) => {
      res
        .status(validate.validateDataType(data).code)
        .json(validate.validateDataType(data));
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "id": 1,
      "name": "CHILE",
      "shortName": "CHL"
    },
    {
      "id": 2,
      "name": "COLOMBIA",
      "shortName": "COL"
    },
    {
      "id": 3,
      "name": "ECUADOR",
      "shortName": "ECU"
    }
  ]
}
```

> Example error response

```json
{
  "code": 400,
  "error": {
    "message": "La consulta solo admite parametros númericos"
  }
}
```

## /v1/countries/:idcountry/

This endpoint retrieves a specific country.

> Definition

```
GET http://localhost:3900/v1/countries/1
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/1'
```

```javascript
const showSpecificCountries = (req, res) => {
  const idCountry = req.params.idCountry;
  Countries.getCountryById(idCountry)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "id": 1,
      "name": "CHILE",
      "shortName": "CHL"
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter | Description | Data type |
| --------- | ----------- | --------- |
| idcountry | Country id  | Int       |

## /v1/countries/:idcountry/regions

This endpoint retrieves all regions on database.

> Definition

```
GET http://localhost:3900/v1/countries/1/regions
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/1/regions'
```

```javascript
const showRegions = (req, res) => {
  const idCountry = req.params.idCountry;
  Regions.getAll(idCountry)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "regions": [
        {
          "id": 25,
          "name": "Región de Arica y Parinacota"
        },
        {
          "id": 26,
          "name": "Región de Tarapacá"
        },
        {
          "id": 27,
          "name": "Región de Antofagasta"
        },
        {
          "id": 28,
          "name": "Región de Atacama"
        },
        {
          "id": 29,
          "name": "Región de Coquimbo"
        },
        {
          "id": 30,
          "name": "Región de Valparaíso"
        },
        {
          "id": 31,
          "name": "Región Metropolitana de Santiago"
        },
        {
          "id": 32,
          "name": "Región del Libertador Gral. Bernardo O'Higgins"
        },
        {
          "id": 33,
          "name": "Región del Maule"
        },
        {
          "id": 34,
          "name": "Región de Ñuble"
        },
        {
          "id": 35,
          "name": "Región del Biobío"
        },
        {
          "id": 36,
          "name": "Región de la Araucanía"
        },
        {
          "id": 37,
          "name": "Región de los Ríos"
        },
        {
          "id": 38,
          "name": "Región de los Lagos"
        },
        {
          "id": 39,
          "name": "Región de Aysén del General Carlos Ibáñez del Campo"
        },
        {
          "id": 40,
          "name": "Región de Magallanes y de la Antártica Chilena"
        }
      ]
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter | Description | Data type |
| --------- | ----------- | --------- |
| idcountry | Country id  | Int       |

## /v1/countries/:idcountry/regions/:idregion

This endpoint retrieves a specific region.

> Definition

```
GET http://localhost:3900/v1/countries/1/regions/38
```

> Example request

```shell

curl --location --request GET 'localhost:3900/v1/countries/1/regions/38'
```

```javascript
const showSpecificRegions = (req, res) => {
  const idCountry = req.params.idCountry;
  const idRegion = req.params.idRegion;
  Regions.getRegionById(idCountry, idRegion)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "regions": [
        {
          "id": 38,
          "name": "Región de los Lagos"
        }
      ]
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter | Description | Data type |
| --------- | ----------- | --------- |
| idcountry | Country id  | Int       |
| idregion  | Region id   | Int       |

## /v1/countries/:idcountry/regions/:idregion/provinces

This endpoint retrieves all provinces of a specific region in database.

> Definition

```
GET http://localhost:3900/v1/countries/1/regions/38/provinces
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/1/regions/38/provinces'
```

```javascript
const showProvinces = (req, res) => {
  const idCountry = req.params.idCountry;
  const idRegion = req.params.idRegion;
  Provinces.getAll(idCountry, idRegion)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "provinces": [
        {
          "id": 267,
          "name": "Chiloé"
        },
        {
          "id": 268,
          "name": "Osorno"
        },
        {
          "id": 266,
          "name": "Llanquihue"
        },
        {
          "id": 269,
          "name": "Palena"
        }
      ]
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter | Description | Data type |
| --------- | ----------- | --------- |
| idcountry | Country id  | Int       |
| idregion  | Region id   | Int       |

## /v1/countries/:idcountry/regions/:idregion/province/:idprovince

This endpoint retrieves a specific province of a region in database.

> Definition

```
GET http://localhost:3900/v1/countries/1/regions/38/provinces/266
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/1/regions/38/provinces/266'
```

```javascript
const showSpecificProvinces = (req, res) => {
  const idCountry = req.params.idCountry;
  const idRegion = req.params.idRegion;
  const idProvince = req.params.idProvince;
  Provinces.getProvinceById(idCountry, idRegion, idProvince)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": {
    "description": [
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      },
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      },
      {
        "code": "Z945",
        "description": "Trasplante de piel"
      }
    ]
  }
}
```

> Example response when the two conditions fits

```json
{
  "code": 200,
  "data": [
    {
      "provinces": [
        {
          "id": 266,
          "name": "Llanquihue"
        }
      ]
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter  | Description | Data type |
| ---------- | ----------- | --------- |
| idcountry  | Country id  | Int       |
| idregion   | Region id   | Int       |
| idprovince | Province id | Int       |

## /v1/countries/:idcountry/regions/:idregion/province/:idprovince/cities/

This endpoint retrieves all cities of a province in database.

> Definition

```
GET localhost:3900/v1/countries/1/regions/38/provinces/266/cities
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/1/regions/38/provinces/266/cities'
```

```javascript
const showCities = (req, res) => {
  const idCountry = req.params.idCountry;
  const idRegion = req.params.idRegion;
  const idProvince = req.params.idProvince;
  Cities.getAll(idCountry, idRegion, idProvince)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "provinces": [
        {
          "name": "Llanquihue",
          "cities": [
            {
              "id": 1693,
              "name": "Calbuco"
            },
            {
              "id": 1699,
              "name": "Maullín"
            },
            {
              "id": 1697,
              "name": "Los Muermos"
            },
            {
              "id": 1694,
              "name": "Cochamó"
            },
            {
              "id": 1700,
              "name": "Puerto Varas"
            },
            {
              "id": 1695,
              "name": "Fresia"
            },
            {
              "id": 1698,
              "name": "Llanquihue"
            },
            {
              "id": 1692,
              "name": "Puerto Montt"
            },
            {
              "id": 1696,
              "name": "Frutillar"
            }
          ]
        }
      ]
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter  | Description | Data type |
| ---------- | ----------- | --------- |
| idcountry  | Country id  | Int       |
| idregion   | Region id   | Int       |
| idprovince | Province id | Int       |

## /v1/countries/:idcountry/regions/:idregion/province/:idprovince/cities/:idcity/

This endpoint retrieves a specific city of a province in database.

> Definition

```
GET http://localhost:3900/v1/countries/1/regions/38/provinces/266/cities/1692
```

> Example request

```shell
curl --location --request GET 'localhost:3900/v1/countries/1/regions/38/provinces/266/cities/1692'
```

```javascript
const showSpecificCities = (req, res) => {
  const idCountry = req.params.idCountry;
  const idRegion = req.params.idRegion;
  const idProvince = req.params.idProvince;
  const idCities = req.params.idCities;
  Cities.getCityById(idCountry, idRegion, idProvince, idCities)
    .then((data) => {
      let response = validate.validateDataType(data);
      if (response.code === 200)
        return res.status(response.code).json(response);
      return res.status(response.code).json(response);
    })
    .catch((error) => {
      res.status(validate.catchError().code).json(validate.catchError());
    });
};
```

> Example response

```json
{
  "code": 200,
  "data": [
    {
      "provinces": [
        {
          "name": "Llanquihue",
          "cities": [
            {
              "id": 1692,
              "name": "Puerto Montt"
            }
          ]
        }
      ]
    }
  ]
}
```

> Example validation error response

```json
{
  "code": 400,
  "error": {
    "message": "No hay data disponible"
  }
}
```

### Query Parameters

| Parameter  | Description | Data type |
| ---------- | ----------- | --------- |
| idcountry  | Country id  | Int       |
| idregion   | Region id   | Int       |
| idprovince | Province id | Int       |
| idcity     | City id     | Int       |

## /readiness

This endpoint retrieves the readiness probe result.

> Definition

```
GET http://localhost:3900/readiness
```

> Example request

```shell
curl -X GET "http://localhost:3900/readiness" -H  "accept: application/json"
```

```javascript
async function readinessCallback(res) {
  if (!(await validations.readinessProbe())) {
    return res.status(500).send({ message: "readiness ERROR" });
  }
  return res.status(200).send({ message: "readiness OK" });
}
```

> Example response

```json
{
  "message": "readiness OK"
}
```

> Example error response

```json
{
  "message": "readiness ERROR"
}
```

## /liveness

This endpoint retrieves the liveness probe result.

> Definition

```
GET http://localhost:3900/liveness
```

> Example request

```shell
curl -X GET "http://localhost:3900/liveness" -H  "accept: application/json"
```

```javascript
async function livenessCallback(res) {
  if (!(await validations.livenessProbe())) {
    return res.status(500).send({ message: "liveness ERROR" });
  }
  return res.status(200).send({ message: "liveness" });
}
```

> Example response

```json
{
  "message": "liveness"
}
```
