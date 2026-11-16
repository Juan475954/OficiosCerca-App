## Descripción del Cambio
Resume de forma clara y concisa los cambios introducidos por este Pull Request. Explica qué problema resuelve o qué funcionalidad agrega.

## Tipo de Cambio
Marca con una `[x]` las casillas que correspondan:
- [ ] Corrección de error (Bug fix que no rompe compatibilidad previa)
- [ ] Nueva funcionalidad (Feature que no rompe compatibilidad previa)
- [ ] Cambio importante / Breaking change (Corrección o funcionalidad que altera el funcionamiento existente)
- [ ] Mejora o actualización de documentación
- [ ] Refactorización o mejora de rendimiento
- [ ] Tarea de configuración o actualización de dependencias (Chore)

## Pantallas o Módulos Modificados
- [ ] Autenticación / Registro
- [ ] Cliente (Búsqueda, Solicitud, Seguimiento, Calificación, Historial)
- [ ] Trabajador (Bandeja, Servicio Activo, Perfil, Reseñas)
- [ ] Repositorio / ViewModel / Supabase
- [ ] Componentes comunes / Tema

## Pruebas Realizadas
Describe las pruebas ejecutadas para validar los cambios:
- [ ] Pruebas unitarias locales ejecutadas con éxito (`./gradlew testDebugUnitTest`)
- [ ] Verificación manual en emulador de Android (API especificada: ____)
- [ ] Verificación manual en dispositivo físico
- [ ] Comprobación de compatibilidad con modo offline (MockData)

## Capturas de Pantalla (si aplica a UI en Jetpack Compose)
| Antes | Después |
| :---: | :---: |
| *(Captura)* | *(Captura)* |

## Lista de Verificación (Checklist)
- [ ] Mi código sigue la guía de estilo oficial de Kotlin y Jetpack Compose.
- [ ] No he introducido credenciales, tokens ni archivos `.env` en el commit.
- [ ] He actualizado la documentación correspondiente si mis cambios así lo requieren.
- [ ] Mis cambios no generan nuevas advertencias en el build de Gradle.
- [ ] He referenciado los issues relacionados (ej. `Closes #12`).
