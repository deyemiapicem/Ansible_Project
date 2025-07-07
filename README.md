# Kubernetes Setup using Vagrant, Ansible, and SSH

This guide describes how to set up a single-node Kubernetes control plane on a `web` VM using Vagrant and Ansible.

---

## Prerequisites
- Vagrant
- VirtualBox
- Ansible
- SSH client

---

## Steps

### 1. Validate and Start Vagrant Machines

```bash
vagrant validate
vagrant up
```

Ensure all machines (e.g., controller, web, db) are up and running.

### 2. Verify Network Configuration

On **each machine**, run:

```bash
ip addr
```

Make sure all machines have IPs in the same subnet, e.g., `192.168.33.x`.

---

### 3. Set Up SSH Access from Controller to Other Machines

On the **controller machine**:

```bash
ssh-keygen  # Accept defaults
cd ~/.ssh
ls          # Confirm id_rsa and id_rsa.pub exist
```

Copy the public key to each target machine (e.g., web):

```bash
ssh-copy-id vagrant@192.168.33.10
```

Repeat for any other VMs.

---

### 4. Create Ansible Hosts File

Example content of your `hosts` file:

```ini
[webservers]
192.168.33.10
```

---

### 5. Write Kubernetes Installation Playbook

Save your Kubernetes installation playbook as `k8s_master_webservers.yml`.

See the content inside `install_k8s.yml` or tailor it to install Kubernetes and initialize the control plane on the `web` host.

---

### 6. Run the Ansible Playbook

```bash
ansible-playbook -i hosts k8s_master_webservers.yml
```

This installs all Kubernetes components and sets up the control plane on the `web` node.

---

### 7. Verify Kubernetes Setup

On the **web node**:

#### Check if control plane initialized:

```bash
ls /etc/kubernetes/admin.conf
```

#### Set up `kubectl` access:

```bash
export KUBECONFIG=/home/vagrant/.kube/config
```

#### Verify node status:

```bash
kubectl get nodes
```

Expected output:

```text
NAME   STATUS   ROLES           AGE   VERSION
web    Ready    control-plane   Xd    v1.33.X
```

---

## You're now ready to deploy workloads on your Kubernetes node! 🎉
