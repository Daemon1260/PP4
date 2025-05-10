# PP4

## Goal

In this exercise you will:

* Use SSH to connect to remote servers from WSL, macOS, or Linux shells, understanding the handshake and authentication process.
* Generate an Ed25519 SSH key pair and explain the concept of digital signatures.
* Configure your local SSH client via the `~/.ssh/config` file for streamlined access.
* Securely copy files between local and remote hosts using `scp`, including local-to-remote, remote-to-local, and remote-to-remote transfers.
* Automate startup tasks on the remote server by writing a shell script that runs at login and explaining the role of `~/.bashrc` vs. `~/.profile`.

**Important:** Start a stopwatch when you begin and work uninterruptedly for **90 minutes**. Once time is up, stop immediately and record exactly where you paused.

---

## Workflow

1. **Fork** this repository
2. **Modify & commit** your solution
3. **Submit your link for Review**

---

## Prerequisites

* Several starter repos are available here:
  [https://github.com/orgs/STEMgraph/repositories?q=SSH%3A](https://github.com/orgs/STEMgraph/repositories?q=SSH%3A)
* Consult the SSH and SCP man-pages for detailed options and explanations:

  * `man ssh`
  * `man scp`

---

## Tasks

### Task 1: SSH Login

**Objective:** Establish an SSH connection and observe each stage of the process.

1. From your local shell (WSL, macOS Terminal, or Linux), log into the `vorlesungsserver` (or any other remote machine of your choice, e.g. your own raspberry pi):

   ```bash
   ssh youruser@remotehost
   ```
2. Carefully observe and note each step:

   * **TCP connection** to port 22 on `remotehost`.
   * **SSH protocol handshake**: key exchange and algorithm negotiation.
   * **Authentication**: public-key or password exchange.
   * **Shell allocation**: your remote session starts.
3. After login, exit the session with `exit`.

**Provide:**

```bash
# 1) The exact ssh command you ran
# 2) A detailed, step-by-step explanation of what happened at each stage
```
1) ssh enoermerich@Daemon1260
2) -Es wurde eine TCP-Verbindung zu Port 22 von Daemon1260 aufgebaut.
   -Derr SSH-Client und der Server führten eine art Handshake durch (erstellen von Verschlüsselung und Austausch temporärer Schlüssel).
   -Die Authentifizierung erfolgte per Passwort.
   -Nach erfolgreicher Anmeldung wurde ein Bash-Shell auf dem Remote-Server geöffnet.
   mit dem Befehl "exit" wurde die Verbindung korrekt beendet.
---

### Task 2: Ed25519 Key Pair

**Objective:** Create a secure key pair and explain how digital signatures verify identity.

1. Generate an Ed25519 SSH key pair:

   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```

   * Accept the default file location (`~/.ssh/id_ed25519`). Or provide the `-f <filepath>` option additionally.
   * Enter a passphrase when prompted (optional).
2. Locate and inspect your `id_ed25519` (private key) and `id_ed25519.pub` (public key).
3. Install your key on the remote machine (e.g. `vorlesungsserver`.
4. Explain in writing:

   * How the **private key** is used to sign challenges.
   * How the **public key** on the server verifies signatures without revealing the private key.
   * Why Ed25519 is preferred (performance, security).

-Der private Schlüssel bleibt sicher auf deinem lokalen Rechner gespeichert.beim verbinden mit SSH, sendet der Server eine Art zufällige Zeichenkette. Mein SSH-Client signiert diese mit meinem privaten Schlüssel. Diese Signatur ist einmalig und kann nur mit dem 
 privaten Schlüssel erstellt werden.
-Der öffentliche Schlüssel liegt auf dem Server in der Datei ~/.ssh/authorized_keys.Wenn der Server deine Signatur bekommt, verwendet er den öffentlichen Schlüssel, um die Echtheit zu prüfen.Dabei wird nicht der private Schlüssel übertragen oder offengelegt.
 Der Server kann mit dem öffentlichen Schlüssel nur verifizieren, dass die Signatur korrekt ist – aber er kann sie nicht selbst erzeugen.
-ED25519 gilt als moderner Standard in der asymetrischen Kyografie. Er bietet sehr hohe Sicherheit und benötigt webiger Rechenleistung, sowie kleinere Schlüsselgrößen.

**Provide:**

```bash
# 1) The ssh-keygen command you ran
# 2) The file paths of the generated keys
# 3) Your written explanation (3–5 sentences) of the signature process
```
1) ssh-keygen -t ed25519 -C rene.noermerich@stud.thga.de
2) Enter file in which to save the key (/home/enoermerich/.ssh/id_ed25519):
3) Bei einem Login über SSH sendet der Server eine zufällige Zeichenkette an meinen lokalen Rechner. Mein SSH-Client verwendet den privaten Schlüssel ~/.ssh/id_ed25519, um diese Zeichenkette mit meiner Passphrase zu signieren. Der Server überprüft diese Signatur mithilfe des öffentlichen Schlüssels, der in ~/.ssh/authorized_keys hinterlegt ist. Ist die Signatur gültig, wird der Zugriff gewährt. Ohne das ein Passwort eingegeben werden muss, Dabei bleibt der private Schlüssel stets sicher auf meinem Gerät gespeichert.
---

### Task 3: SSH Config File

**Objective:** Simplify SSH commands via `~/.ssh/config`.

1. Open (or create) `~/.ssh/config` in `vim`.
2. Add entries for your hosts, for example:

   ```text
   Host my-remote
       HostName remote.example.com
       User youruser
       IdentityFile ~/.ssh/id_ed25519

   Host backup-server
       HostName backup.example.com
       User backupuser
       Port 2222
       IdentityFile ~/.ssh/id_ed25519_backup
   ```
3. Save and close the file, then test:

   ```bash
   ssh my-remote
   ssh backup-server
   ```
4. Explain:

   * How SSH reads `~/.ssh/config` and matches hosts.
   * The difference between `HostName` and `Host`.
   * How aliases prevent long commands.

-Beim eingeben von ssh Server-PP4, schaut SSH zuerst in der Datei ~/.ssh/config nach, ob es einen passenden Host-Eintrag gibt.
 Wenn ja, nutzt es die dort definierten Einstellungen wie Benutzername, Hostname, Port oder den Pfad zum privaten Schlüssel.
 -Host ist eine eigen erstellter Name, HostName ist der tatsächliche Rechnername oder die IP-Adresse zu dem/der sich SSH verbinden soll.
 -Ohne ~/.ssh/config müsste man alles was dort hinterlegt ist in einen langen code schreiben, dies erspart uns ~/.ssh/config sodass man nur z.B. ssh Server-PP4 reicht.

**Provide:**

```text
# 1) The full contents of your ~/.ssh/config
# 2) A short explanation (3–4 sentences) of how the config simplifies connections
```
1)
    Host Server-PP4
      Hostname Daemon1260
      User enoermerich
      Identyfile ~/.ssh/id_ed25519

    Host Backup Server-PP4
      Hostname Daemon1260
      User enoermerich
      Identyfile ~/.ssh/id.ed25519_Backup Server-PP4
2) in der Datei "~/.ssh/config" werden die Verbindungsdetails gespeichert. Durch eingabe der Host (Server-PP4 oder Backup Server-PP4) wird eine Verbindung erstellt.
---

### Task 4: SCP File Transfers

**Objective:** Practice copying files securely using `scp`.

1. **Local → Remote**:

   ```bash
   scp /path/to/localfile.txt youruser@remotehost:~/destination/
   ```
2. **Remote → Local**:

   ```bash
   scp youruser@remotehost:~/remotefile.log ./local_destination/
   ```
3. **Remote → Remote** (between two directories on the same remote host):

   ```bash
   scp -r youruser@remotehost:/path/dir1 youruser@remotehost:/path/dir2
   ```
4. For each command:

   * Verify file timestamps and sizes after transfer, using `ls -la`
   * Note any flags you used (e.g., `-r`, `-P` for port).
5. Explain:

   * How `scp` initiates an SSH session for each transfer.
   * The role of encryption in protecting data in transit.

**Provide:**

```bash
# 1) Each scp command you ran
# 2) Any flags or options used
# 3) A brief explanation (2–3 sentences) of scp’s mechanism
```
1)
  -scp ~/Dokumente/Test.txt enoermerich@Daemon1260 :~/backups/
  -scp enoermerich@Daemon1260:~/backups/Test.txt ~/Downloads/
  -scp -r enoermerich@Daemon1260:/var/log enoermerich@Daemon1260:/home/max/log_backup

2)
  -r: für rekursives Kopieren (also komplette Verzeichnisse mit Unterordnern, keine weiteren Flags notwendig in den obigen Beispielen)

3)
  scp verwendet das SSH-Protokoll, um Dateien verschlüsselt zwischen Computern zu übertragen. Dabei wird automatisch eine sichere Verbindung aufgebaut – so sind sowohl die Datei als auch Benutzername und Passwort geschützt.
  Die Authentifizierung läuft genauso ab wie bei einer normalen SSH-Verbindung, was scp einfach und sicher zugleich macht.
  
---
Zeitlich kam ich nur bis hier hin!

### Task 5: Login Shell Script & Profile Explanation

**Objective:** Automate commands at login and understand shell initialization files.

1. On the **remote** server, create a script `~/login_tasks.sh` containing at least three commands you find useful (e.g., `echo "Welcome $(whoami)"`, `uptime`, `ls ~/projects`). You may either use `vim` or try the following to create a file from your commandline directely:

   ```bash
   cat << 'EOF' > ~/login_tasks.sh
   #!/usr/bin/env bash
   echo "Welcome $(whoami)! Today is $(date)."
   uptime
   ls ~/projects
   EOF
   chmod +x ~/login_tasks.sh
   ```

> The files content should be something akin to:
> ```bash
> #!/usr/bin/env bash
> echo "Welcome $(whoami)! Today is $(date)."
> uptime
> ls ~/projects
> ```

2. Append to your `~/.bashrc` (or `~/.profile` if using a login shell) a line to source this script on each new session:

   ```bash
   echo "source ~/login_tasks.sh" >> ~/.bashrc
   ```
3. Log out and log back in to trigger the script.
4. Explain:

   * The difference between `~/.bashrc` and `~/.profile` (interactive vs. login shells).
   * Why and when each file is read.
   * How sourcing differs from executing.

**Provide:**

```bash
# 1) The contents of login_tasks.sh
# 2) The lines you added to ~/.bashrc or ~/.profile
# 3) Your explanation (3–5 sentences) of shell init files and sourcing vs. executing
```

---

**Remember:** Stop working after **90 minutes** and record where you stopped.
