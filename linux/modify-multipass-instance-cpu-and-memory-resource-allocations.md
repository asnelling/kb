---
description: >-
  Modify the amount of memory (RAM) and CPU cores allotted to an existing
  multipass instance.
---

# Modify multipass instance CPU and memory resource allocations

These instructions serve as a workaround until [multipass](https://github.com/canonical/multipass) includes built-in support for adjusting resource allocation. This functionality is planned and tracked in [#756](https://github.com/canonical/multipass/issues/756).

1. Stop the instance\
   `$ multipass stop <instance-name>`
2. Stop the multipass service\
   `$ sudo systemctl stop snap.multipass.multipassd.service`
3. Open `multipassd-vm-instances.json` for editing\
   `$ sudo -e /var/snap/multipass/common/data/multipassd/multipassd-vm-instances.json`
4. Under the key matching the name of the instance being configured, tweak `mem_size` (in bytes) and/or `num_cores` as desired.
5. Start the multipass service\
   `$ sudo systemctl start snap.multipass.multipassd.service`
6. Start the modified instance\
   `$ multipass start <instance-name>`
7. Verify resources are as configured\
   `$ multipass shell <instance-name>`\
   `$ free -h`\
   `$ nproc`

Procedure sourced from [#1158](https://github.com/canonical/multipass/issues/1158).
