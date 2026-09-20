# Modelo Entidad-Relación

Diagrama original en notación de Chen: [`CrisjonDiagramaEntidadRelacion.drawio.svg`](diagramas/CrisjonDiagramaEntidadRelacion.drawio.svg)

## Diagrama (notación pata de gallo)

```mermaid
erDiagram
    CLIENTE ||--o{ ORDEN : hace
    ORDEN ||--|{ JOYA : contiene
    JOYA ||--o{ FOTO : retrata
    JOYA ||--o{ TRANSICION : registra
    ESTACION ||--o{ TRANSICION : ocurre_en
    EMPLEADO ||--o{ TRANSICION : realizada_por
    PROVEEDOR_EXTERNO ||--o{ TRANSICION : enviada_a
    CLIENTE ||--o| PARTICULAR : "es un"
    CLIENTE ||--o| DISTRIBUIDOR : "es un"

    CLIENTE {
        string id_cliente PK
        string nombre_razon_social
        string direccion "compuesto"
        string telefono "multivaluado"
        string email "multivaluado"
    }

    PARTICULAR {
        string id_cliente FK
        string tipo_solicitud
    }

    DISTRIBUIDOR {
        string id_cliente FK
        string nombre_comercial
        string nit_ruc
        string codigo_acceso_portal
        string persona_contacto
    }

    ORDEN {
        string id_orden PK
        string id_cliente FK
        date fecha_recepcion
        date fecha_estimada_entrega
        date fecha_entrega
        string tipo_trabajo
        string estado_actual
        string codigo_qr
        string firma_digital
    }

    JOYA {
        string id_orden FK
        int numero_pieza PK "clave parcial - entidad debil"
        string tipo_joya
        string descripcion
        string talla
        string estado_pieza
        string material "multivaluado"
    }

    FOTO {
        int numero_pieza FK
        int numero_foto PK "clave parcial - entidad debil"
        string url_foto
        datetime fecha_captura
    }

    TRANSICION {
        int numero_pieza FK
        int numero_transicion PK "clave parcial - entidad debil"
        string id_estacion FK
        string id_empleado FK
        string id_proveedor FK
        datetime fecha_hora_escaneo
    }

    ESTACION {
        string id_estacion PK
        string nombre_estacion
        int orden_secuencia
    }

    EMPLEADO {
        string id_empleado PK
        string nombre
        string rol
        string usuario_sistema
    }

    PROVEEDOR_EXTERNO {
        string id_proveedor PK
        string nombre
        string especialidad
        string contacto "multivaluado"
    }
```

## Notas del modelo

**Entidades débiles.** `Joya`, `Foto` y `Transicion` son débiles: su clave primaria es parcial y
depende de la entidad fuerte a la que se identifican (`Orden`, `Joya` y `Joya` respectivamente).
Mermaid no tiene notación para entidad débil; en el diagrama de arriba se representan con relación
identificadora y la clave parcial marcada en el comentario.

**Especialización.** `Cliente` se especializa en `Particular` y `Distribuidor` (disjunta).
Mermaid no soporta la notación de triángulo, así que aparecen como entidades relacionadas 1:0..1.

**Atributos multivaluados.** `telefono`, `email`, `material` y `contacto` son multivaluados. Al pasar
a modelo relacional, cada uno se convierte en una tabla aparte con clave foránea a su entidad.

**Atributo compuesto.** `direccion` de `Cliente` se descompone en `tipo_via_principal`,
`numero_via_principal`, `numero_via_secundaria`, `complemento` y `referencia_domicilio`.

**Cardinalidades.** Todas las relaciones del modelo son 1:N en el sentido indicado por la flecha
(un cliente hace muchas órdenes, una orden contiene muchas joyas, una joya tiene muchas fotos y
muchas transiciones, y cada transición ocurre en una estación, la realiza un empleado y puede
enviarse a un proveedor externo).
