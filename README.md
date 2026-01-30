# Ansible Galaxy Role Project - NFS Setup

## What is This Project?

This project shows how to use **Ansible Galaxy** to install a custom role from GitHub and use it to setup NFS (Network File System) server.

## Project Structure

```
ansible-role-demo/
├── requirements.yaml          # Tells Galaxy where to download the role
├── inventory.ini             # Which servers to run on (localhost)
├── ansible.cfg              # Ansible settings
├── playbooks/
│   └── setup-nfs.yaml       # Playbook that uses the role
└── roles/                   # Galaxy installs roles here
    └── nfs/                # Downloaded from GitHub
```

## What Each File Does

### requirements.yaml
Points to the GitHub repository where the nfs role is stored.
```yaml
roles:
  - name: nfs
    src: https://github.com/TaingAnKorm/nfs-role
    version: master
```

### inventory.ini
Tells Ansible to run on localhost (your computer).

### ansible.cfg
Configuration that tells Ansible where to find roles.

### playbooks/setup-nfs.yaml
A playbook that uses the nfs role to setup an NFS server.

### roles/nfs/
The actual role code, downloaded from GitHub by ansible-galaxy.

## Step-by-Step Testing

### Step 1: Install the Role from GitHub
```bash
cd ~/ansible-role-demo
ansible-galaxy install -r requirements.yaml -p ./roles
```

**What this does**: Downloads the nfs role from your GitHub repository into `./roles/nfs/`

**Expected output**:
```
Starting galaxy role install process
- extracting nfs to ./roles/nfs
- nfs (master) was installed successfully
```

### Step 2: Verify the Role is Installed
```bash
ls -la roles/
```

**What to see**: A folder named `nfs/`

```bash
ls -la roles/nfs/
```

**What to see**: Folders like `meta/`, `tasks/`, `defaults/`, `handlers/`

### Step 3: Check the Requirements File
```bash
cat requirements.yaml
```

**What to see**: The GitHub URL pointing to your nfs-role repository

### Step 4: Check the Playbook
```bash
cat playbooks/setup-nfs.yaml
```

**What to see**: A playbook that uses the nfs role in server mode

### Step 5: Check What Tasks the Role Has
```bash
cat roles/nfs/tasks/main.yml
```

**What to see**: Main tasks that decide server or client mode

```bash
cat roles/nfs/tasks/setup-nfs-server.yml
```

**What to see**: Tasks to setup NFS server

### Step 6: (Optional) Run the Playbook
```bash
ansible-playbook playbooks/setup-nfs.yaml
```

**What happens**:
1. Installs NFS server packages
2. Creates export directory (`/srv/nfs`)
3. Configures NFS exports
4. Starts NFS server service

### Step 7: (If you ran Step 6) Check NFS Status
```bash
systemctl status nfs-kernel-server
```

**What to see**: Should show "active (running)"

### Step 8: (If you ran Step 6) Verify NFS Export
```bash
showmount -e localhost
```

**What to see**: List of exported directories

## Testing Without Running the Playbook

### Test 1: Install Role
```bash
ansible-galaxy install -r requirements.yaml -p ./roles
```

### Test 2: Show Role Structure
```bash
ls -la roles/nfs/
cat roles/nfs/meta/main.yml
```

### Test 3: Dry Run (Check Mode)
```bash
ansible-playbook playbooks/setup-nfs.yaml --check
```

This shows what WOULD happen without actually doing it.

## Useful Commands

```bash
# Install role from GitHub
ansible-galaxy install -r requirements.yaml -p ./roles

# Reinstall role (force)
ansible-galaxy install -r requirements.yaml -p ./roles --force

# List installed roles
ansible-galaxy list -p ./roles

# Remove installed role
rm -rf roles/nfs

# Run playbook
ansible-playbook playbooks/setup-nfs.yaml

# Dry run (check mode)
ansible-playbook playbooks/setup-nfs.yaml --check

# Check NFS status (after install)
systemctl status nfs-kernel-server

# Show NFS exports
showmount -e localhost
```

## How It Works

1. You run `ansible-galaxy install -r requirements.yaml -p ./roles`
2. Galaxy reads requirements.yaml
3. Galaxy downloads the role from GitHub
4. Role is saved to `./roles/nfs/`
5. You run the playbook
6. Playbook uses the role from `./roles/nfs/`
7. NFS server gets configured!

## The Two GitHub Repositories

### nfs-role
**URL**: https://github.com/TaingAnKorm/nfs-role  
**What**: The actual role code (tasks, meta, defaults, handlers)  
**Location on your computer**: `~/nfs-role`

### ansible-role-demo
**URL**: https://github.com/TaingAnKorm/ansible-role-demo  
**What**: This demo project  
**Location on your computer**: `~/ansible-role-demo`

## What the NFS Role Does

The role can work in two modes:

### Server Mode (default)
- Installs NFS server packages
- Creates export directory
- Configures `/etc/exports`
- Starts NFS server
- Exports the shares

### Client Mode
- Installs NFS client packages
- Creates mount directory
- Mounts NFS share from server

## Troubleshooting

**Problem**: `ansible-galaxy: command not found`  
**Fix**: Install Ansible: `pip3 install ansible`

**Problem**: Role not found after install  
**Fix**: Make sure you're in `~/ansible-role-demo` and used `-p ./roles`

**Problem**: Permission denied  
**Fix**: The playbook uses `become: true` which needs sudo

## What You Learned

✅ How to use Ansible Galaxy to install roles from GitHub  
✅ How to use `-p ./roles` to install to current directory  
✅ How to create requirements.yaml  
✅ How to use roles in playbooks  
✅ How to configure NFS with Ansible
