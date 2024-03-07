# VS code

## Edit remote-SSH target list

Open the config file `~/.ssh/config` , and edit

```
Host 192.168.253.1
  HostName 192.168.253.1
  User name

Host 192.168.253.2
  HostName 192.168.253.2
  User name
```

to

```
Host server1
  HostName 192.168.253.1
  User name

Host server2
  HostName 192.168.253.2
  User name
```

After making the changes, they are considered as new remote connection, and the hisotry target list will be removed (see [Issue #7474](https://github.com/microsoft/vscode-remote-release/issues/7474))

According to [How can I manually edit the list of recently opened files in VS Code? - Stack Overflow](https://stackoverflow.com/questions/74701706/how-can-i-manually-edit-the-list-of-recently-opened-files-in-vs-code), we can edit `state.vscdb` to rename the saved history

On Windows:

```python
import json
import sqlite3

# open vscode database
db = sqlite3.connect(r'C:\Users\<Username>\AppData\Roaming\Code\User\globalStorage\state.vscdb')
remote_raw = db.execute("SELECT [value] FROM ItemTable WHERE  [key] = 'ms-vscode-remote.remote-ssh'").fetchone()[0]
remote = json.loads(remote_raw)

# hostnames to be renamed
name_dict = {'192.168.253.1': 'server1',
             '192.168.253.2': 'server2'}

# rename
for old, new in name_dict.items():
    remote['folder.history.v1'][new] = remote['folder.history.v1'][old]

remote_raw = json.dumps(remote)
db.execute(f"UPDATE ItemTable SET [value] = '{remote_raw}' WHERE key = 'ms-vscode-remote.remote-ssh'")
db.commit()
db.close()
```
