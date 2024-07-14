# Triggers

1.- Crear un función y un trigger para validar que el numero de cedula del cliente tenga 10 números (no letras) en la tabla cliente.:

```
CREATE OR REPLACE FUNCTION validate_cedula_length()
RETURNS TRIGGER AS $$
BEGIN
  IF LENGTH(NEW.cedula) != 10 OR NEW.cedula ~ '[^\d]' THEN
    RAISE EXCEPTION 'El campo cedula debe tener exactamente 10 dígitos numéricos.';
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
Captura:

![Captura de pantalla 2024-07-13 230856](https://github.com/user-attachments/assets/bb29cdbb-2bac-43b7-9f38-26d69be55f24)


```
CREATE TRIGGER validate_cedula_length_before_insert
BEFORE INSERT ON clientes
FOR EACH ROW
EXECUTE PROCEDURE validate_cedula_length();

```
Captura:

![Captura de pantalla 2024-07-13 231240](https://github.com/user-attachments/assets/6f80118b-e33b-4877-95d6-21d2fa0320c6)

```
SELECT * FROM information_schema.triggers
WHERE event_object_table = 'clients';
```
Captura:

![Captura de pantalla 2024-07-13 231457](https://github.com/user-attachments/assets/55441fb5-315e-42ca-ba96-d08ea4b8f67b)

2.- Crear un función y un trigger para que cada vez que se inserte un nuevo registro en la tabla item se disminuya el stock de la tabla product.


```
CREATE OR REPLACE FUNCTION update_stock_on_item_insert()
RETURNS TRIGGER AS $$
BEGIN
  UPDATE productos
  SET cantidad_en_stock = cantidad_en_stock - NEW.quantity
  WHERE id = NEW.product_id;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
Captura:


![Captura de pantalla 2024-07-13 231750](https://github.com/user-attachments/assets/66869318-4c74-4126-8160-608abf8fd4ee)


```
CREATE TRIGGER update_stock_after_item_insert
AFTER INSERT ON item
FOR EACH ROW
EXECUTE PROCEDURE update_stock_on_item_insert();
```
Captura:


![Captura de pantalla 2024-07-13 232137](https://github.com/user-attachments/assets/d8bfeec4-c06c-4719-89b3-9d5ec847a213)


```
SELECT *
FROM information_schema.triggers
WHERE trigger_name = 'update_stock_after_item_insert'
  AND event_object_table = 'item';
```
Captura:

![Captura de pantalla 2024-07-13 232228](https://github.com/user-attachments/assets/718dc869-0361-49e9-967e-4c7885854a86)


3.- Crear un función y un trigger para la tabla invoice donde valide que el campo create_at sea del año actual (fecha sistema).


```
CREATE OR REPLACE FUNCTION validate_invoice_create_date()
RETURNS TRIGGER AS $$
BEGIN
  IF EXTRACT(YEAR FROM NEW.create_at) <> EXTRACT(YEAR FROM CURRENT_DATE) THEN
    RAISE EXCEPTION 'La fecha de creación debe ser del año actual.';
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
Captura:

![image](https://github.com/user-attachments/assets/435613e6-9911-452c-9f5b-f2a13632872e)


```
SELECT *
FROM information_schema.triggers
WHERE trigger_name = 'validate_invoice_create_date_before_insert'
  AND event_object_table = 'invoice';
```
Captura:

![image](https://github.com/user-attachments/assets/528edb7f-2ed6-4f61-8c61-01f7b8254e9c)


4.- Crear un función y un trigger para la tabla client y validar que el correo tenga un @.

```
CREATE OR REPLACE FUNCTION validate_email_format()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.email !~ '@' THEN
    RAISE EXCEPTION 'El correo electrónico debe contener "@".';
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
Captura:

![image](https://github.com/user-attachments/assets/388312ed-3974-4d29-b7a4-f3bdc0590bad)



```
SELECT *
FROM information_schema.triggers
WHERE trigger_name = 'validate_email_format_before_insert'
  AND event_object_table = 'clients';
```
Captura:

![image](https://github.com/user-attachments/assets/8be0209c-5d96-4ecf-b39f-a692a96f1252)
