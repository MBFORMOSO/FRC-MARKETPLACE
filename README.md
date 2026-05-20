# FRC Marketplace

Marketplace de plugins para el equipo FRC de MercadoLibre.

## Cómo agregar este marketplace en Cowork

```
/plugin marketplace add TU-ORG/frc-marketplace-repo
```

## Cómo instalar el plugin FRC Toolkit

```
/plugin install frc-marketplace@frc-marketplace
```

## Plugins disponibles

| Plugin | Descripción | Skills |
|---|---|---|
| `frc-marketplace` | Kit completo del equipo FRC | data-analytics, doc-generator, task-automator, code-reviewer |

## Cómo agregar nuevos plugins

1. Crear la carpeta del plugin en `plugins/nuevo-plugin/`
2. Agregar `.claude-plugin/plugin.json` y las skills correspondientes
3. Registrar el plugin en `.claude-plugin/marketplace.json` bajo `plugins`
4. Hacer commit y push — los usuarios verán el nuevo plugin al ejecutar `/plugin marketplace update`
