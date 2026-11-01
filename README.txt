RAILWAY DEPLOYMENT — PERSISTENT BUILD
======================================

GITHUB
------
1. Extract this ZIP.
2. Upload/replace all files in the ROOT of your GitHub repository.
3. Keep the entrypoint named exactly HAHAHOST.py.
4. Procfile is included: worker: python HAHAHOST.py

RAILWAY START COMMAND
---------------------
python HAHAHOST.py

PERSISTENT DATA (IMPORTANT)
---------------------------
1. In Railway, open the service -> Volumes -> Add Volume.
2. Set the mount path to: /data
3. In service Variables, add: DATA_DIR=/data
4. Keep BOT_TOKEN and ADMIN_ID variables for the HOSTING/CONTROLLER bot.
5. Redeploy.

The SQLite database and uploaded bot files/logs are stored under DATA_DIR.
They survive normal redeploys/restarts as long as the Railway Volume remains
attached at /data. Do not delete the volume if you want to keep the data.
The program attempts a one-time copy of any legacy hosting_data.db and
hosted_files folder found in the old app working directory, without
overwriting files already present in the persistent volume.

BOT TOKEN SEPARATION
--------------------
Uploaded bot processes do not inherit the hosting controller's Telegram
token environment variables. If a Telegram token is literally present in
the uploaded .py source, it is detected and supplied as BOT_TOKEN to that
child process. If the uploaded bot stores its token in a config file,
encoded value, or expects another environment variable name, configure that
bot's own token in its code/config instead.

PERFORMANCE / RELIABILITY
-------------------------
The existing bot features and database schema are preserved. Uploads with
the same filename are stored in separate unique folders. The database uses
SQLite WAL mode and a 30-second busy timeout.

IMPORTANT
---------
Railway container local storage is ephemeral. A Railway Volume is required
for data persistence. Keep one running replica for this SQLite-based setup;
do not run multiple service replicas against the same SQLite database.
Uploaded Python scripts run in the same Railway container and are not fully
sandboxed. Only host code you trust.
