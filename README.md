# MDMCOIN blockchain — PostgreSQL sync scripts

A set of scripts to download and update Waves blockchain history data into a PostgreSQL 11.x database.

## Usage

1. Clone the repository, install dependencies.
   ```bash
   npm install
   ```
2. Create `config.yml` file in the project, using `config.example.yml` for reference.

3. In PostgreSQL, create empty database. (All Tables)
```bash
wget https://github.com/mdmcoin/mdmcoin-blockchain-postgres-sync/blob/master/migrations/sql/20190917130306_initial_schema/up.sql
```
```bash
run all up.sql files of this folder - https://github.com/mdmcoin/mdmcoin-blockchain-postgres-sync/tree/master/migrations/sql
```



4. Set environment variable `MIGRATE` to `true` (or just run crawler like this: `MIGRATE=true npm run ...`), it will apply initial and all additional migrations to yours database.

5. ⬇️ To download a range of blocks to database:

   ```bash
   npm run download {start} {end},
   # for example
   npm run download 1 100000
   ```

   Blocks from the range get inserted in a single transaction, so either all get inserted, or none. In our experience ranges of 10000—100000 work best.

6. 🔄 To keep your database up-to-date:
   ```bash
   npm run updateComposite
   ```
   or use SCREEN
    ```bash
   screen -d -m -S MDM-postgres-sync  node /mdmcoin-blockchain-postgres-sync/src/updateComposite/run.js
   ```
   
   This is a continuous script, so you may want to run it in the background. We recommend using some kind of process manager (e.g. `pm2`) to restart the process on crash.

## Migrations

1. Create migration:
   ```bash
   ./node_modules/.bin/knex --migrations-directory migrations migrate:make $MIGRATION_NAME
   ```
2. Migrate latest:
   ```bash
   ./node_modules/.bin/knex migrate:latest --client postgresql --connection postgresql://$PGUSER:$PGPASSWORD@$PGHOST:$PGPORT/$PGDATABASE --migrations-directory migrations
   # OR
   npm run migrate -- --connection postgresql://$PGUSER:$PGPASSWORD@$PGHOST:$PGPORT/$PGDATABASE
   ```
