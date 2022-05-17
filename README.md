# Api Rest PrismaDB para mission BackEnd LaunchX 

## Implementacion realizada con las dependencias y base de datos:
* Prisma DB
* Express
* PostgreSQL

### Instalacion de dependencias
```
npm install express --save-dev
npm install prisma --save-dev
```

## Utilizacion de Prisma DB
### Inicializacion de prisma en el proyecto:
```
npx prisma init
```
### Configuracion acceso a base de datos de PostgreSQL
* Crear archivo .env en el cual se debera de realizar la siguiente configuracion con los datos de usuario, contraseña direccion y base de datos:
```
DATABASE_URL="postgresql://TUUSUARIO:PASSWORD@localhost:5432/explorers_api?schema=public"
```
* Creacion de modelo en directorio `prisma/schema.prisma`
```mermaid
 classDiagram
      class Especie{
          + id (Int)
          + name(varchar)
          + username(varchar(255))
          + mission(varchar(255))
          + azureCertification(BooleanField)
          + dateCreated (DateTime now())
          + lastUpdated (DateTime)
      }
````
```
model Explorer {
  id                 Int @id  @default(autoincrement())
  name               String   @unique
  username           String   @db.VarChar(255)
  mission            String   @db.VarChar(255)
  azureCertification Boolean  @default(false)
  dateCreated        DateTime @default(now())
  lastUpdated        DateTime @updatedAt
}
```
* Aplicacion de migraciones con el comando:
```
npx prisma migrate dev --name init
```
* Archivo `prisma/seed.js` generado para insertar datos de forma automatizada en la base de datos con el comando:
```
node prisma/seed.js
```

## `Express` con implementacion de `Prisma` para CRUD con la base de datos. 
### Importacion de prisma:
```
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();
```
### Comandos utilizados en peticiones api:
* prisma.model.findMany : Busca en la tabla multiples datos y los retorna en forma de objeto.
* prisma.model.findUnique : Busca en la tabla un dato delimitado por una propiedad del mismo modelo y lo retorna en forma de objeto.
* prisma.model.create : Crea un registro en la tabla con los datos ingresados.
* prisma.model.update : Actualiza un registro filtrado en la tabla.
* prisma.model.delete : Elimina registros filtrados con los parametros recibidos.
### Implementacion en apis:
* Consultas:
```javascript
app.get('/explorers', async (req, res) => {
  const allExplorers =  await prisma.explorer.findMany({});
  res.json(allExplorers);
});

app.get('/explorers/:id', async (req, res) => {
  const id = req.params.id;
  const explorer = await prisma.explorer.findUnique({where: {id: parseInt(id)}});
  res.json(explorer);
});
```
* Creacion:
```javascript
app.post('/explorers', async (req, res) => {
  const explorer = {
    name: req.body.name,
    username: req.body.username,
    mission: req.body.mission
   };
  const message = 'Explorer creado.';
  await prisma.explorer.create({data: explorer});
  return res.json({message});
});
```
* Actualizacion:
```javascript
app.put('/explorers/:id', async (req, res) => {
	const id = parseInt(req.params.id);

	await prisma.explorer.update({
		where: {
			id: id
		},
		data: {
			mission: req.body.mission
		}
	})

	return res.json({message: "Actualizado correctamente"});
});
```
* Borrado:
```javascript
app.delete('/explorers/:id', async (req, res) => {
	const id = parseInt(req.params.id);
	await prisma.explorer.delete({where: {id: id}});
	return res.json({message: "Eliminado correctamente"});
});
```
