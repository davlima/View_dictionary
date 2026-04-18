
# 📘 Oracle Data Dictionary & Dynamic Views — Wiki Prática

## 📌 Visão Geral

O Oracle possui dois grandes grupos de metadados:

### 🟢 Data Dictionary (Views Estáticas)

* Baseadas em tabelas internas (ex: `SYS.USER$`)
* Contêm **estrutura e configuração**
* Prefixos:

  * `USER_` → objetos do usuário
  * `ALL_` → objetos acessíveis
  * `DBA_` → todos objetos do banco
  * `CDB_` → multitenant

---

### 🔵 Dynamic Performance Views (Views Dinâmicas)

* Prefixos:

  * `V$` → instância local
  * `GV$` → cluster (RAC)
* Contêm **estado em tempo real**
* Baseadas em memória (SGA)

---

# 🟩 1. USUÁRIOS E SEGURANÇA

## 🔹 Views Estáticas

### 📌 Usuários do banco

```sql
SELECT username, account_status, created
FROM dba_users;
```

👤 Permissão: `DBA`

```sql
SELECT username
FROM all_users;
```

👤 Permissão: `ALL`

---

### 📌 Privilégios de sistema

```sql
SELECT grantee, privilege
FROM dba_sys_privs;
```

👤 Permissão: `DBA`

---

### 📌 Privilégios de objeto

```sql
SELECT grantee, owner, table_name, privilege
FROM dba_tab_privs;
```

👤 Permissão: `DBA`

---

## 🔹 Base interna (avançado)

```sql
SELECT user#, name, astatus
FROM sys.user$;
```

👤 Permissão: `SYSDBA`

---

# 🟦 2. OBJETOS (TABELAS, ÍNDICES)

## 🔹 Tabelas

```sql
SELECT table_name
FROM user_tables;
```

👤 Permissão: `USER`

```sql
SELECT owner, table_name
FROM all_tables;
```

👤 Permissão: `ALL`

```sql
SELECT owner, table_name
FROM dba_tables;
```

👤 Permissão: `DBA`

---

## 🔹 Índices

```sql
SELECT index_name, table_name
FROM user_indexes;
```

👤 Permissão: `USER`

```sql
SELECT owner, index_name, table_name
FROM dba_indexes;
```

👤 Permissão: `DBA`

---

# 🟨 3. STORAGE (TABLESPACES E DATAFILES)

## 🔹 Tablespaces

```sql
SELECT tablespace_name, status
FROM dba_tablespaces;
```

👤 Permissão: `DBA`

---

## 🔹 Datafiles

```sql
SELECT file_name, tablespace_name, bytes/1024/1024 AS MB
FROM dba_data_files;
```

👤 Permissão: `DBA`

---

## 🔹 Uso de espaço

```sql
SELECT tablespace_name, SUM(bytes)/1024/1024 MB
FROM dba_segments
GROUP BY tablespace_name;
```

👤 Permissão: `DBA`

---

# 🟥 4. BACKUP & RECOVERY

## 🔹 RMAN / Arquivos

```sql
SELECT name, checkpoint_change#
FROM v$datafile;
```

👤 Permissão: `SELECT_CATALOG_ROLE` ou `DBA`

---

```sql
SELECT group#, status
FROM v$log;
```

👤 Permissão: `DBA`

---

```sql
SELECT sequence#, applied
FROM v$archived_log;
```

👤 Permissão: `DBA`

---

# 🟪 5. PERFORMANCE (CRÍTICO)

## 🔹 Sessões ativas

```sql
SELECT sid, serial#, username, status
FROM v$session;
```

👤 Permissão: `DBA`

---

## 🔹 SQL em execução

```sql
SELECT sql_id, executions, elapsed_time
FROM v$sql;
```

👤 Permissão: `DBA`

---

## 🔹 Esperas

```sql
SELECT event, total_waits
FROM v$system_event;
```

👤 Permissão: `DBA`

---

## 🔹 Locks

```sql
SELECT sid, type, lmode, request
FROM v$lock;
```

👤 Permissão: `DBA`

---

# 🟫 6. MULTITENANT (CDB/PDB)

## 🔹 PDBs

```sql
SELECT pdb_name, status
FROM cdb_pdbs;
```

👤 Permissão: `DBA`

---

## 🔹 Containers

```sql
SELECT name, con_id
FROM v$containers;
```

👤 Permissão: `DBA`

---

# ⚫ 7. EXECUTION PLAN

```sql
SELECT * 
FROM v$sql_plan
WHERE sql_id = '&sql_id';
```

👤 Permissão: `DBA`

---

# 🧠 BOAS PRÁTICAS

* Prefira:

  * `USER_` → scripts portáveis
  * `ALL_` → aplicações
  * `DBA_` → administração

* Evite acesso direto a:

  * `SYS.*` (exceto troubleshooting avançado)

---

# 🚀 DICA DE OURO (PROVA + PRODUÇÃO)

👉 Toda `DBA_`, `ALL_`, `USER_` vem de tabelas internas (`SYS`)

👉 Toda `V$` mostra estado em tempo real

Se você entende isso:

* passa na prova
* resolve problema real

---

# 📌 SUGESTÃO DE ESTRUTURA NO GITHUB

```
oracle-dictionary-wiki/
│
├── README.md
├── users.md
├── objects.md
├── storage.md
├── performance.md
├── backup.md
├── multitenant.md
```

