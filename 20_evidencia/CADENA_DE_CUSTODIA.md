# Cadena de custodia de la evidencia
| ID | Archivo | SHA-256 | Fecha y hora (UTC) | Obtenido por | Método de obtención | Sistema origen |
|---|---|---|---|---|---|---|
| E01-01 | E01_baseline/contenedores.json | 7eb61b8099aa5d97a7a5c02ab4221cc78cd97fe1e8aeb012492ff115f52bf13d | 2026-08-26 21:47:13 | Auditor | docker compose ps json | si084-lab |
| E01-02 | E01_baseline/imagenes.tsv | 3acb22e679ec95a09705b799ddeca40b1cdab5cc2cab8ec299560483b8a8c56e | 2026-08-26 21:47:13 | Auditor | docker images digests | si084-lab |
| E01-03 | E01_baseline/puertos.tsv | 4d173e6b29782327d43c9e205fc2fca8f49125741318c3cf62bfa3ad0746a253 | 2026-08-26 21:47:13 | Auditor | docker ps ports | si084-lab |
| E01-04 | E01_baseline/compose_efectivo.yml | dd8fa9584cdc592b409d86610e03460cbd69cb200cc13d5efcd6e83a0d778c35 | 2026-08-26 21:47:13 | Auditor | docker compose config | si084-lab |
| E01-05 | E01_baseline/usuarios_postgres.txt | af32b889fbae13a312669ffa58b8881b9d6fe3176903e7ff2f8d5eedbeff5e02 | 2026-08-26 21:47:13 | Auditor | docker exec psql du | si084_db |
| E01-06 | E01_baseline/env_db.json | 1f221c0edd57bc23ead3800f00170872d0b9f0d7ab4e32dc0904fd6ee3cb865f | 2026-08-26 21:47:13 | Auditor | docker inspect Config.Env | si084_db |
